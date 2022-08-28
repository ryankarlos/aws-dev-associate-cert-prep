Signature Version 4 (SigV4) is the process to add authentication information to AWS API requests sent by HTTP. For security, 
most requests to AWS must be signed with an access key. The access key consists of an access key ID and secret access key, 
which are commonly referred to as your security credentials. 

**How Signature Version 4 works**

* Create a canonical request.
* Use the canonical request and additional metadata to create a string for signing.
* Derive a signing key from your AWS secret access key. Then use the signing key, and the string from the previous step, to create a signature.
* Add the resulting signature to the HTTP request in a header or as a query string parameter.

When an AWS service receives the request, it performs the same steps that you did to calculate the signature you sent in 
your request. AWS then compares its calculated signature to the one you sent with the request. If the signatures match, 
the request is processed. If the signatures don't match, the request is denied.

