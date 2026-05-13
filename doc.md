# Loan API Documentation

**Complete API Reference**  
**Version:** v1  
**Modules:** `loans` & `app_admin`  
**Last Reviewed:** Final Exhaustive Review

---

## 1. User & Merchant Endpoints

**Base URL:** `/api/v1/loans/`

These endpoints are used by borrowers (Merchants and Agents). `IsLoanOwner` ensures users can only access their own data.

| Method | Endpoint                        | Description                                      | Required Permissions                  |
|--------|---------------------------------|--------------------------------------------------|---------------------------------------|
| **GET**    | `/`                             | List all loans belonging to the user            | `IsAuthenticated`, `IsMerchantOrAgent` |
| **POST**   | `/`                             | **Submit Application** – Apply for a new loan    | `IsAuthenticated`, `IsMerchant`       |
| **GET**    | `/{pk}/`                        | View full details of a specific loan             | `IsAuthenticated`, `IsLoanOwner`      |
| **GET**    | `/products/`                    | List available loan products                     | `IsAuthenticated`, `IsMerchant` \| `IsAgent` |
| **POST**   | `/simulate/`                    | Calculate projected repayment schedule           | `IsAuthenticated`, `IsMerchant`       |
| **POST**   | `/{pk}/repay/`                  | Submit repayment (Wallet or External)            | `IsAuthenticated`, `IsLoanOwner`      |
| **POST**   | `/{pk}/disbursement-destination/` | Set payout destination (Wallet/Bank/MoMo)     | `IsAuthenticated`, `IsLoanOwner`      |
| **GET**    | `/{pk}/schedules/`              | View installment schedule                        | `IsAuthenticated`, `IsLoanOwner`      |
| **GET**    | `/stats/`                       | Personal loan health statistics                  | `IsAuthenticated`, `IsMerchantOrAgent` |
| **GET**    | `/guarantors/`                  | List registered personal guarantors              | `IsAuthenticated`, `IsMerchantOrAgent` |
| **POST**   | `/guarantors/`                  | Register a new guarantor                         | `IsAuthenticated`, `IsMerchantOrAgent` |
| **PUT**    | `/guarantors/{pk}/`             | Update guarantor details                         | `IsAuthenticated`, `IsMerchantOrAgent` |
| **GET**    | `/activity/`                    | Audit trail across all loans                     | `IsAuthenticated`, `IsMerchantOrAgent` |
| **GET**    | `/{pk}/activity/`               | Audit trail for a specific loan                  | `IsAuthenticated`, `IsMerchantOrAgent` |

---

## 2. Agent Float Endpoints

**Base URL:** `/api/v1/loans/agent/`

Dedicated to the 24-hour mobile money agent float product.

| Method | Endpoint                        | Description                                      | Required Permissions          |
|--------|---------------------------------|--------------------------------------------------|-------------------------------|
| **GET**    | `/account/`                     | View float eligibility and current limits        | `IsAuthenticated`, `IsAgent` |
| **GET**    | `/account/countdown/`           | Live 24-hour repayment countdown                 | `IsAuthenticated`, `IsAgent` |
| **POST**   | `/request-offer/`               | Request a transient float offer                  | `IsAuthenticated`, `IsAgent` |
| **POST**   | `/offers/{pk}/accept/`          | Accept offer and trigger instant disbursement    | `IsAuthenticated`, `IsAgent` |
| **POST**   | `/offers/{pk}/decline/`         | Decline a float offer                            | `IsAuthenticated`, `IsAgent` |

---

## 3. Administrative Control Endpoints

**Base URL:** `/api/v1/admin/loans/`

Internal staff tools for underwriting, disbursement, and management.

| Method     | Endpoint                          | Description                                              | Required Permissions                          |
|------------|-----------------------------------|----------------------------------------------------------|-----------------------------------------------|
| **GET**        | `/`                               | Master list of all loans                                 | `IsAuthenticated`, `IsAdmin`                  |
| **PATCH**      | `/{pk}/`                          | Change loan status (e.g., to Underwriting)               | `IsAuthenticated`, `IsAdmin`                  |
| **PATCH**      | `/{pk}/status/`                   | Update status (`Approved` / `Rejected`)                  | `IsCreditOfficer` (Approved) / `IsAdmin`      |
| **POST**       | `/{pk}/disburse/`                 | Trigger payout / fund transfer                           | `IsAuthenticated`, `IsFinanceOfficer`         |
| **POST**       | `/{pk}/waive-penalty/`            | Manually waive penalties                                 | `IsAuthenticated`, `IsAdmin`                  |
| **GET**        | `/{pk}/ledger-entries/`           | View double-entry ledger records                         | `IsAuthenticated`, `IsAdmin` \| `IsFinanceOfficer` |
| **GET**        | `/{pk}/audit-trails/`             | Full lifecycle audit logs                                | `IsAuthenticated`, `IsAdmin`                  |
| **GET**        | `/{pk}/schedules/`                | Admin view of repayment schedule                         | `IsAuthenticated`, `IsAdmin`                  |
| **GET**        | `/{pk}/guarantors/`               | View guarantors for a loan                               | `IsAuthenticated`, `IsAdmin` \| `IsCreditOfficer` |
| **GET**        | `/users/{id}/guarantors/`         | All guarantors used by a user                            | `IsAuthenticated`, `IsAdmin` \| `IsCreditOfficer` |
| **GET/POST**   | `/products/`                      | Create and manage loan products                          | `IsAuthenticated`, `IsAdmin`                  |
| **PATCH**      | `/products/{id}/{status}/`        | Activate / Deactivate product                            | `IsAuthenticated`, `IsAdmin`                  |
| **GET**        | `/payments/`                      | Master list of all platform payments                     | `IsAuthenticated`, `IsAdmin`                  |

---

## 4. Collections & Delinquency Management

**Base URL:** `/api/v1/admin/collections/`

| Method     | Endpoint                          | Description                                              | Required Permissions                  |
|------------|-----------------------------------|----------------------------------------------------------|---------------------------------------|
| **GET**        | `/queue/`                         | Delinquent loans ready for case opening                  | `IsAuthenticated`, `IsCollectionsOfficer` |
| **GET/POST**   | `/cases/`                         | List cases or open new collections case                  | `IsAuthenticated`, `IsCollectionsOfficer` |
| **PATCH**      | `/cases/{pk}/`                    | Update case status / add notes                           | `IsAuthenticated`, `IsCollectionsOfficer` |
| **GET/POST**   | `/cases/{id}/promises/`           | Record Promise-to-Pay (PTP) commitments                  | `IsAuthenticated`, `IsCollectionsOfficer` |

---

## 5. Account & Agent Management

| Method | Endpoint                                           | Description                                      | Required Permissions     |
|--------|----------------------------------------------------|--------------------------------------------------|--------------------------|
| **GET**    | `/admin/agents/{id}/loan-account/`                 | View / Manage agent lending profile              | `IsAuthenticated`, `IsAdmin` |
| **POST**   | `/admin/agents/{id}/loan-account/suspend/`         | Suspend agent's borrowing ability                | `IsAuthenticated`, `IsAdmin` |
| **POST**   | `/admin/agents/{id}/loan-account/lift-suspension/` | Restore agent's borrowing ability                | `IsAuthenticated`, `IsAdmin` |

---

**Notes:**
- All endpoints require `IsAuthenticated` as a baseline.
- Custom permissions (`IsLoanOwner`, `IsMerchant`, `IsAgent`, `IsAdmin`, `IsCreditOfficer`, etc.) are enforced.
- This is the final verified list after exhaustive review of `views.py`, `loan_views.py`, and `collections_views.py`.

---
*Generated for Nicholas – Accra, Ghana*