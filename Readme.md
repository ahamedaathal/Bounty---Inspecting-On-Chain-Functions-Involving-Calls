# Bounty - Inspecting On-Chain Functions Involving Calls

## Introduction

- **Protocol Name**: Uniswap
- **Category**: DeFi
- **Smart Contract**: UniswapV2ERC20

## Function Analysis

- **Function Name**: permit

- **Block Explorer Link**: (https://etherscan.io/address/0x7885e359a085372ebcf1ed6829402f149d02c600#code)

- **Function Code**:

```
function permit(
    address owner,
    address spender,
    uint value,
    uint deadline,
    uint8 v,
    bytes32 r,
    bytes32 s
) external {
    require(deadline >= block.timestamp, 'UniswapV2: EXPIRED');
    bytes32 digest = keccak256(
        abi.encodePacked(
            '\x19\x01',
            DOMAIN_SEPARATOR,
            keccak256(abi.encode(PERMIT_TYPEHASH, owner, spender, value, nonces[owner]++, deadline))
        )
    );
    address recoveredAddress = ecrecover(digest, v, r, s);
    require(recoveredAddress != address(0) && recoveredAddress == owner, 'UniswapV2: INVALID_SIGNATURE');
    _approve(owner, spender, value);
}

```

- **Used Encoding/Decoding or Call Method**: `abi.encodePacked`

- **Here's the critical part of the function where abi.encodePacked is used**:

```
bytes32 digest = keccak256(
    abi.encodePacked(
        '\x19\x01',
        DOMAIN_SEPARATOR,
        keccak256(abi.encode(PERMIT_TYPEHASH, owner, spender, value, nonces[owner]++, deadline))
    )
);

```

This digest is then used to verify the signature and, upon successful verification, the \_approve function is called to set the allowance.

## Explanation

- **Purpose**:
  The permit function allows for approvals to be made via signatures, following the EIP-2612 standard. This enables a user to approve an allowance for a spender without sending an on-chain transaction, thus saving gas fees and providing a more seamless user experience.

- **Detailed Usage**:
  The function leverages abi.encodePacked to concatenate and hash the EIP712 domain separator and the permit type hash with the provided parameters (owner, spender, value, nonces[owner]++, and deadline). This hash is then signed off-chain and verified on-chain using the ecrecover function to retrieve the signer's address from the provided signature components (v, r, s).

- **Impact**:
  The use of abi.encodePacked in the permit function allows for efficient encoding of multiple parameters into a single hash, which is essential for the EIP-2612 permit functionality. This method contributes significantly to the contract's functionality by enabling gasless approvals, improving user experience, and maintaining security through cryptographic signatures. This feature is particularly impactful in DeFi protocols, where frequent interactions with ERC-20 tokens are common, and reducing the number of required on-chain transactions can lead to significant cost savings and enhanced usability.

Here's the critical part of the function where abi.encodePacked is used:

bytes32 digest = keccak256(
abi.encodePacked(
'\x19\x01',
DOMAIN_SEPARATOR,
keccak256(abi.encode(PERMIT_TYPEHASH, owner, spender, value, nonces[owner]++, deadline))
)
);
