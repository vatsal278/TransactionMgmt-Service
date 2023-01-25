# Transaction Managment Service-Design

## Transaction Managment Service Endpoints

DbSchema:
```
+----------------+---------------+------+-----+-------------------+-----------------------------------------------+
| Field          | Type          | Null | Key | Default           | Extra                                         |
+----------------+---------------+------+-----+-------------------+-----------------------------------------------+
| transaction_id | varchar(255)  | NO   | PRI | NULL              |                                               |
| account_number | int           | NO   |     | NULL              |                                               |
| user_id        | varchar(255)  | NO   |     | NULL              |                                               |
| amount         | decimal(18,2) | NO   |     | 0.00              |                                               |
| transfer_to    | varchar(255)  | NO   |     | NULL              |                                               |
| created_at     | timestamp     | NO   | MUL | CURRENT_TIMESTAMP | DEFAULT_GENERATED                             |
| updated_at     | timestamp     | YES  |     | CURRENT_TIMESTAMP | DEFAULT_GENERATED on update CURRENT_TIMESTAMP |
| status         | varchar(255)  | NO   |     | NULL              |                                               |
| type           | varchar(255)  | NO   |     | NULL              |                                               |
| comment        | varchar(255)  | YES  |     | NULL              |                                               |
+----------------+---------------+------+-----+-------------------+-----------------------------------------------+

```
Html Template File:
```
<table class="tg">
    <tbody>
    <tr>
        <td class="tg-fymr">TransferFrom</td>
        <td class="tg-fymr">TransferTo</td>
    </tr>
    <tr>
        <td class="tg-0pky">{{.Name}}<br>{{.TransferFromAccountNumber}}</td>
        <td class="tg-0pky">{{.TransferToAccountNumber}}</td>
    </tr>
    <tr>
        <td class="tg-fymr">Amount</td>
        <td class="tg-fymr">Type</td>
    </tr>
    <tr>
        <td class="tg-0pky">{{.Amount}}</td>
        <td class="tg-0pky">{{.Type}}</td>
    </tr>
    <tr>
        <td class="tg-fymr">TransactionId</td>
        <td class="tg-fymr">Status</td>
    </tr>
    <tr>
        <td class="tg-0pky">{{.TransactionId}}</td>
        <td class="tg-0pky">{{.Status}}</td>
    </tr>
    <tr>
        <td class="tg-fymr">Date</td>
        <td class="tg-fymr">Comment</td>
    </tr>
    <tr>
        <td class="tg-0pky">{{.Date}}</td>
        <td class="tg-0pky">{{.Comment}}</td>
    </tr>
    </tbody>
</table>
```
## List Transactions
A user hits this endpoint in order to view their transactions. It uses pagenation to list a specefic number of records at once.For viewing a specefic transaction we can specify the transaction id of that record. 
There will be jwt token containing userid in cookie for authentication of user.
#### Specification:
Method: `GET`

Path (list of transaction): `/transactions`

query parameters:
- `page` : specefic page which user wants to use
- `limit` : total number of records in that page

Request Body: `not required.`

Success to follow response as specified:

Response Header: HTTP 200

Response Body(json):
```json
{
  "status": 200,
  "message": "SUCCESS",
  "data": [{
    "transaction_id":"<full transaction id>",
    "date": "date of transaction" DD-MM-YYY format,
    "amount": <amount of transaction>,
    "status": <Status of transaction ,Approved or Rejected>,
    "type" :<Credit Or Debit type of transaction>,
    "comment":<comment about the transaction>,
    "transfer_from":<account number of own account>,
    "transfer_to":<Name and account number of receiver>
  }]
}
```

## Do Transaction
This endpoint is used to do a new transaction. It is a post endpoint which is used to update the database with latest transaction and its details.
This endpoint stores the transaction data along with the user_id which can be obtained from cookie
#### Specification:
Method: `POST`

Path: `/transaction/new`

Request Body: 
```json
{
  "account_number":"acc_no."
  "amount": total amount of the transaction as float,
  "status":"Approved or Rejected",
  "transafer_to":"name of receiver",
  "comment":"comment if any"
  "type":"debit or credit"
}
```

Success to follow response as specified:

Response Header: HTTP 200

Response Body(json):
```json
{
  "status": 201,
  "message": "SUCCESS",
  "data": nil
}
```

## Download Transaction Details
This endpoint is used to download the transaction detail of a specific transaction as a pdf.
#### Specification:
Method: `GET`

Path: `transactions/download/transaction_id`

Request Body: nil


Success to follow response as specified:

Response Header: HTTP 200

Response Body(pdf):Pdf file will get downloaded

## AccManagementSvc Middlewares

1. ExtractUser: extracts the user_id from the cookie passed in the request and forwards it in the context for downstream processing.
2. Caching middleware
