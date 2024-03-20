# Closing-Account-Balance

This repository contains SQL code to calculate the final account balances of users based on their opening balances and transaction history.

## Problem Statement

You are given a list of users and their opening account balances along with the transactions they've made. Users can conduct transactions among themselves. The task is to calculate the final account balance for each user after considering all transactions.

![day19 sql](https://github.com/bhumikadata/Closing-Account-Balance/assets/131578649/d80db5f8-6de6-4c8f-a3be-44f7c111d9e6)


## Solution Overview

### Common Table Expressions (CTEs):
The solution starts by defining two CTEs (all_transaction and sum_transaction) to handle the calculation logic.

### all_transaction CTE:
This CTE combines all transactions, considering both outgoing and incoming amounts.

It uses the UNION ALL operator to combine two queries:

The first query selects transactions where the user is the sender (from_userid) and negates the amount to represent the outgoing transaction.
The second query selects transactions where the user is the receiver (to_userid) without any negation to represent the incoming transaction.

### sum_transaction CTE:
This CTE aggregates the transactions for each user to calculate the total transaction amount for each user.
It uses the SUM() function to sum the transaction amounts grouped by the user ID.

### Main Query:
The main query joins the users table with the sum_transaction CTE using a left join.

It calculates the closing balance for each user by adding the opening balance from the users table to the transaction amount from the sum_transaction CTE.

It uses COALESCE() to handle cases where there are no transactions for a user (resulting in a NULL transaction amount).
The result of this query is the user ID and the corresponding closing balance.

### Solution SQL Script

```sql
with all_transaction as 
(
select 
  from_userid as user_id,
  amount * -1  as amount
from 
  transactions
union all 
select
  to_userid as user_id,
  amount
from 
  transactions    
)
, sum_transaction as 
(
select 
  user_id,
  sum(amount)  as transaction_amount
from 
  all_transaction 
group by 
  user_id  
)
select 
  u.user_id,
  u.opening_balance + coalesce(s.transaction_amount,0)  as closing_balance
from 
  users  u
left join 
  sum_transaction  s
on u.user_id = s.user_id
