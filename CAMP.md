# CAMP - Crypto Alias Management Protocol

CAMP is a sub-protocol of OpenCap. CAMP is an optional protocol that allows a server to facilitate the creation, updating, and deletion of a user's alias resources. For a server to be CAMP compliant it must also be CAP compliant by serving aliases, CAMP is essentially a "tier 2" protocol built on top of CAP to add some more features. If a server wishes to allow the following operations to users, they must be implmented in the following way via these endpoints:

## POST /v1/users

No Authorization (Public method)

Creates a new user.

Body:
```javascript
{
    "username": "john",
    "password": "mysecretpassword"
}
```

Response:
```javascript
Status: 200
{

}
```

## DELETE /v1/users/:username

Basic Authentication

Deletes the user and all associated addresses.

Response:
```javascript
Status: 200
{

}
```

## PUT /v1/users/{username}/ledgers/{ledger_id}

Basic Authentication

Adds or updates the address for the user of the given ledger id.  
Please note that the authenticated user and the param in the url must match.

Body:
```javascript
{
    "type": 0,
    "address": "YukHsVy/J9VCU5nr9vD7UOu4jxg="
}
```

Response:
```javascript
Status: 200
{

}
```

## DELETE /v1/users/{username}/ledgers/{ledger_id}

Basic Authentication

Deletes the address for the user of the given ledger id.  
Please note that the authenticated user and the param in the url must match.

Response:
```javascript
Status: 200
{

}
```