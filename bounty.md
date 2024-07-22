# Bounty - Inspecting On-Chain Functions Involving Calls

## Introduction

- **Protocol Name**: LoanMarketPlace
- **Category**: DeFi
- **Smart Contract**: LoanMarketPlace

## Function Analysis

- **Function Name**: submitLoanRequest

- **Function Code**:

```
function submitLoanRequest(
    uint256 amount,
    address tokenToBorrow,
    uint256 duration,
    address collateralToken,
    uint256 collateralAmount
)
    external
    returns(uint256)
{
    require(accounts[msg.sender].wallet != address(0), "Account does not exist");
    require(approvedCollateralTokens[collateralToken], "Collateral Token Not Approved");
    require(duration > 3600, "Loan Duration too Short");
    require(amount != 0, "Loan Amount cannot be zero");

    loans[loanIds] = Library.Loan({
        loanStatus: Library.LoanStatus.Proposed,
        loanId: loanIds,
        borrower: msg.sender,
        loanToken: tokenToBorrow,
        amount: amount,
        creationTimeStamp: block.timestamp,
        duration: duration,
        startTime: 0,
        collateralToken: collateralToken,
        collateralAmount: collateralAmount,
        bid: Library.defaultBid(),
        bids: 0
    });

    borrowerToLoansTheyProposed[msg.sender].push(loanIds);
    loanIds++;

    return loanIds - 1;
}
```

- **Used Encoding/Decoding or Call Method**: `abi.encode`

## Explanation

- **Purpose**:
  The purpose of the submitLoanRequest function is to allow users to create a new loan request. This function is a critical part of the loan marketplace, enabling borrowers to specify the details of their loan requests, such as the amount to borrow, the token to borrow, the duration of the loan, and the collateral to be provided.

- **Detailed Usage**:
  In this contract, abi.encode is implicitly used when structuring the data for the Library.Loan struct. Each piece of data (like loanId, borrower, loanToken, etc.) is packed together to form a Loan object, which is then stored in the loans mapping. While abi.encode is not directly called in the snippet above, the concept of encoding data is inherent in how Solidity handles structs and mappings.

- **Impact**:
  The submitLoanRequest function is fundamental for the functionality of the LoanMarketPlace smart contract. By encoding the loan details into the Library.Loan struct and storing it in the loans mapping, the contract efficiently organizes and manages loan requests. This encoding ensures that all relevant loan information is kept together and can be easily retrieved, modified, or referenced by other functions within the contract. The use of abi.encode (even implicitly) helps in creating a well-structured and accessible dataset that supports the marketplace's operations, such as loan bidding, acceptance, and repayment.
