# DemoBenefits
Benefits Application

## Assumptions
- All transactions are in United State Dollars. At this moment, I will not worry about currency conversion and will not design this from international perspective
- Do I need to worry about credit/debit from the account? At this moment, I am only thinking about crediting the account and not about employee OR a third party(example - Gym integration for wellness) withdrawing from the account.
- For this design, I will not consider linking to banks or any financial services. Eventually if 
  we are moving money from one account to another account, I will assume the bank accounts 
  information between company and employee has been linked through.


### Database Schema

1. Company
   - id
   - name
   - type
   - logoUrl
   - created_date
    
2. Employee
   - id
   - firstName
   - lastName
   - email
   - jobTitle
   - address_id (foreign key)
   - company_id (foreign key)
   - imageUrl

3. Address
   - id
   - address1
   - address2
   - city
   - state
   - county
   - country
   - zipcode

4. Account
   - id
   - name
   - type
   - contribution_amount
   - currency
   - cycle
   - company_id (foreign key)
   - employee_id (foreign key)
   - created_date

5. Balance
   - id
   - employee_id (foreign key)
   - account_id (foreign key)
   - amount
   - currency
   - contribution_date
   - updated_date

### APIs

#### POST /v1/api/companies/{companyId}/employees/{employeeId}/account  - create account for an employee of a company for specified benefit

// Request
```
{
name: "health and wellness",
type: "wellness",
cycle: "monthly",
contribution_amount: 250,
currency: "USD"    
}
```

// Response
```
{
employee: "John Doe",
company: "Acme Ltd.",
name: "health and wellness",
type: "wellness",
cycle: "monthly",
contribution_amount: 250
currency: USD
created_date: 07/26/2021  
}
```

#### GET /v1/api/companies/{companyId}/employees/{employeeId}/accounts - get all the accounts for 
an employee of a company

// Response
```
[
{
employee: "John Doe",
company: "Acme Ltd",
name: "health and wellness",
type: "wellness",
cycle: "monthly",
contribution_amount: 250,
currency: "USD",
created_date: 07/26/2021	 
},
{
employee: "John Doe",
company: "Acme Ltd",
name: "parking",
type: "commute",
cycle: "quarterly",
contribution_amount: 150,
currency: "USD",
created_date: 07/26/2021	 
}
]
```

#### POST /v1/api/companies/{companyId}/employees/{employeeId}/accounts/{accountId} - deposit contribution amount per cycle in benefit account for an employee of a company

// Request
```
{
"amount" : 250,
"currency" : "USD",
"cycle" : "monthly"  
}
```

// Response
```
{
"account" : "wellness",
"cycle" : "monthly",
"total_balance" : 2000,
"currency" : "USD",
"last_contribution_date": 08/01/2021
}
```

#### GET /v1/api/companies/{companyId}}/employees/{employeeId}/accounts/{accountId}/balance - get balance information for the benefit account of an employee of a company
// Response
```
{
"account" : "wellness",
"cycle": "monthly",
"total_balance" : 2000,
"currency" : "USD",
"last_contribution_date": 08/01/2021
}
```

#### GET /v1/api/companies/{companyId}/employees/{employeeId}/accounts/{accountId}/balance?last_contribution_date="06/01/2021" - get balance information for benefit account based on last_contribution_date
// Response
```
{
"account" : "wellness",
"cycle": "monthly",
"total_balance" : 1500,
"currency" : "USD",
"last_contribution_date": 06/01/2021   
}
```

#### GET /v1/api/companies/{companyId}/employees/{employeeId}/accounts/{accountId}/balance?from_date=?&to_date=?
#### GET /v1/api/companies/{companyId}/employees/{employeeId}/accounts/{accountId}/balance?year=?


### Test Plan
1. create an account for an employee of a company - Happy Path
2. create an account for non-existent employee id of a correct company id
3. create an account for non-existent employee id of an non-existent company id
4. create an account for an employee of a company with empty contribution cycle (default monthly)
5. create an account for an employee of a company with non-existent contribution cycle
6. create an account for an employee of a company with not allowed contribution amount

7. get all benefit accounts information for an employee of a company
8. get all benefit accounts information for non-existent employee of a company
9. get all benefit accounts information for non-existent employee of a non-existent company

10. deposit contribution amount per cycle in benefit account of an employee of a company
11. deposit contribution amount per cycle in benefit account of an employee of a company - with two requests coming at the same time causing synchronization issue to test locking of the transaction while depositing and calculation balance
12. deposit contribution amount per cycle in benefit account of a non-existent employee of a company
13. deposit contribution amount per cycle in non-existent benefit account of a employee of a company
14. deposit contribution amount per cycle in a benefit account of an employee of a non-existent company
15. deposit contribution amount per cycle in a benefit account of an employee who left the 
    company - if deposited, there should be a mechanism to credit back to company and updating 
    employee account information
16. get contribution summary so far (till last contribution cycle)
17. get contribution summary for a specific contribution date
18. get contribution summary for a year
19. get contribution summary for a date range  
