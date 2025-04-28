---
created: 20250428
summary: "Kerberos is a network authentication protocol designed to provide secure authentication for users and services in a computer network."
---

# kerberos

## content

Kerberos  is a network authentication protocol designed to provide secure authentication for users and services in a computer network.

Kerberos is the default authentication protocol used in Active Directory environments for user logins and service authentication. Many UNIX and Linux systems also implement Kerberos for secure authentication.


### How Kerberos Works 

Here is a high-level overview of how Kerberos authentication typically works: 

1. User Login: The user logs into a client machine (such as a workstation).
2. TGT Request: The client requests a TGT from the Authentication Server (AS) by providing its username.
3. TGT Issuance: The AS verifies the user's credentials (typically against a password) and issues a TGT, which is then stored on the client.
4. Service Request: When the client wants to access a service (e.g., a file server), it presents the TGT to the Ticket Granting Server (TGS) to obtain a service ticket.
5. Service Ticket Issuance : The TGS verifies the TGT and issues a service ticket for the requested service.
6. Accessing the Service: The client presents the service ticket to the target service to access resources, completing the authentication process.
     

## refs

### up

- [[network]]
- [[ad]]
- [[authentification]]

### down

