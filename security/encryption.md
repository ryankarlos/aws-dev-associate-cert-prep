Data protection refers to protecting data while in-transit (as it travels to and from Amazon S3) and at rest (while it is stored on disks in Amazon S3 data centers). You can protect data in transit using Secure Socket Layer/Transport Layer Security (SSL/TLS) or client-side encryption. You have the following options for protecting data at rest in Amazon S3:

###Server-Side Encryption

Request Amazon S3 to encrypt your object before saving it on disks in its data centers and then decrypt it when you download the objects.

You have three mutually exclusive options, depending on how you choose to manage the encryption keys:

**Server-Side Encryption with Amazon S3-Managed Keys (SSE-S3)**

When you use Server-Side Encryption with Amazon S3-Managed Keys (SSE-S3), each object is encrypted with a unique key. As an additional safeguard, it encrypts the key itself with a root key that it regularly rotates. Amazon S3 server-side encryption uses one of the strongest block ciphers available, 256-bit Advanced Encryption Standard (AES-256) GCM, to encrypt your data.

If you need server-side encryption for all of the objects that are stored in a bucket, use a bucket policy. For example, the following bucket policy denies permissions to upload an object unless the request includes the x-amz-server-side-encryption header to request server-side encryption:

**Note**: Server-side encryption encrypts only the object data, not object metadata.

```json
{
  "Version": "2012-10-17",
  "Id": "PutObjectPolicy",
  "Statement": [
    {
      "Sid": "DenyIncorrectEncryptionHeader",
      "Effect": "Deny",
      "Principal": "*",
      "Action": "s3:PutObject",
      "Resource": "arn:aws:s3:::awsexamplebucket1/*",
      "Condition": {
        "StringNotEquals": {
          "s3:x-amz-server-side-encryption": "AES256"
        }
      }
    },
    {
      "Sid": "DenyUnencryptedObjectUploads",
      "Effect": "Deny",
      "Principal": "*",
      "Action": "s3:PutObject",
      "Resource": "arn:aws:s3:::awsexamplebucket1/*",
      "Condition": {
        "Null": {
          "s3:x-amz-server-side-encryption": "true"
        }
      }
    }
  ]
}
```

To request server-side encryption using the object creation REST APIs, provide the x-amz-server-side-encryption request header. 

The following Amazon S3 APIs support this header:

* PUT operations—Specify the request header when uploading data using the PUT API.
* Initiate Multipart Upload—Specify the header in the initiate request when uploading large objects using the multipart upload API .
* COPY operations—When you copy an object, you have both a source object and a target object. 


**Server-Side Encryption with KMS keys Stored in AWS Key Management Service (SSE-KMS)**

Server-Side Encryption with AWS KMS keys (SSE-KMS) is similar to SSE-S3, but with some additional benefits and charges for using this service. 
There are separate permissions for the use of a KMS key that provides added protection against unauthorized access of your objects in Amazon S3. 
SSE-KMS also provides you with an audit trail that shows when your KMS key was used and by whom. Additionally, you can create and manage customer 
managed keys or use AWS managed keys that are unique to you, your service, and your Region. 

AWS KMS uses envelope encryption to further protect your data. Envelope encryption is the practice of encrypting your plaintext data with a data key, and then encrypting that data key with a root key.
If you don't specify a customer managed key, Amazon S3 automatically creates an AWS KMS key in your AWS account the first time that you add an object encrypted with SSE-KMS to a bucket. 
By default, Amazon S3 uses this KMS key for SSE-KMS.

If you want to use a customer managed key for SSE-KMS, create the customer managed key before you configure SSE-KMS. Then, when you configure SSE-KMS for your bucket, specify the 
existing customer managed key. Creating a customer managed key gives you more flexibility and control. For example, you can create, rotate, and 
disable customer managed keys. You can also define access controls and audit the customer managed key that you use to protect your data. 

If you choose to encrypt your data using a KMS key or customer managed key, AWS KMS and Amazon S3 perform the following actions:
* Amazon S3 requests a plaintext data key and a copy of the key encrypted under the specified KMS key.
* AWS KMS generates a data key, encrypts it under the KMS key, and sends both the plaintext data key and the encrypted data key to Amazon S3.
* Amazon S3 encrypts the data using the data key and removes the plaintext key from memory as soon as possible after use.
* Amazon S3 stores the encrypted data key as metadata with the encrypted data.

When you request that your data be decrypted, Amazon S3 and AWS KMS perform the following actions:
* Amazon S3 sends the encrypted data key to AWS KMS.
* AWS KMS decrypts the key by using the same KMS key and returns the plaintext data key to Amazon S3.
* Amazon S3 decrypts the ciphertext and removes the plaintext data key from memory as soon as possible.


**Note**:When you use an AWS KMS key for server-side encryption in Amazon S3, you must choose a symmetric encryption KMS key. 
Amazon S3 supports only symmetric encryption KMS keys and not asymmetric keys. 

**Server-Side Encryption with Customer-Provided Keys (SSE-C)**

With Server-Side Encryption with Customer-Provided Keys (SSE-C), you manage the encryption keys and Amazon S3 manages the encryption, as it writes to disks, and decryption, when you access your objects.
Using server-side encryption with customer-provided encryption keys (SSE-C) allows you to set your own encryption keys. With the encryption key you provide as part of your request, Amazon S3 manages the encryption 
as it writes to disks and decryption when you access your objects. Therefore, you don't need to maintain any code to perform data encryption and decryption. The only thing you do is manage the encryption keys you provide.

When you upload an object, Amazon S3 uses the encryption key you provide to apply AES-256 encryption to your data and removes the encryption key from memory.
When you retrieve an object, you must provide the same encryption key as part of your request. Amazon S3 first verifies that the encryption key you provided matches and then decrypts the object before returning the object data to you.

When you use SSE-C, you must provide encryption key information using the following request headers:

|Name|Description|
|:--:|:---------:|
|x-amz-server-side-encryption-customer-algorithm| Use this header to specify the encryption algorithm. The header value must be "AES256".|
|x-amz-server-side-encryption-customer-key| Use this header to provide the 256-bit, base64-encoded encryption key for Amazon S3 to use to encrypt or decrypt your data.|
|x-amz-server-side-encryption-customer-key-MD5| Use this header to provide the base64-encoded 128-bit MD5 digest of the encryption key according to RFC 1321. Amazon S3 uses this header for a message integrity check to ensure that the encryption key was transmitted without error.|

###Client-Side Encryption

Client-side encryption is the act of encrypting your data locally to ensure its security as it passes to the Amazon S3 service.
The Amazon S3 service receives your encrypted data; it does not play a role in encrypting or decrypting it.

To enable client-side encryption, you have the following options:

**Use a key stored in AWS Key Management Service (AWS KMS)**

When uploading an object, the client first sends a request to AWS KMS for a new symmetric encryption key that it can use to
encrypt their object data. AWS KMS returns two versions of a randomly generated data key:
* A plaintext version of the data key that the client uses to encrypt the object data.
* A cipher blob of the same data key that the client uploads to Amazon S3 as object metadata.

When the client needs the data, the encrypted object can be downloaded from Amazon S3 along with the cipher blob version of the data key stored as object metadata. 
The client then sends the cipher blob to AWS KMS to get the plaintext version of the data key so that it can decrypt the object data.

**Use a key that you store within your application**

When uploading the object, you provide a client-side root key to the Amazon S3 encryption client. 
The client uses the root key only to encrypt the data encryption key that it generates randomly.

The following steps describe the process:
* The Amazon S3 encryption client generates a one-time-use symmetric encryption key (also known as a data encryption key or data key) locally. It uses the data key to encrypt the data of a single Amazon S3 object. The client generates a separate data key for each object.
* The client encrypts the data encryption key using the root key that you provide. The client uploads the encrypted data key and its material description as part of the object metadata. The client uses the material description to determine which client-side root key to use for decryption.
* The client uploads the encrypted data to Amazon S3 and saves the encrypted data key as object metadata (x-amz-meta-x-amz-key) in Amazon S3.

The  client downloads the encrypted object from Amazon S3. Using the material description from the object's metadata, the client determines which root key to use to decrypt the data key. The client uses that root key to decrypt the data key and then uses the data key to decrypt the object.
The client-side root key that you provide can be either a symmetric encryption key or a public/private key pair. 


## Data in Transit

AWS strongly recommends encrypting data in transit from one system to another, including resources within and outside of AWS.

When you create an AWS account, a logically isolated section of the AWS Cloud—the Amazon Virtual Private Cloud (Amazon VPC—is provisioned to it. 
There, you can launch AWS resources in a virtual network that you define. You have complete control over your virtual networking environment, 
including selecting your own IP address range, creation of subnets, and configuration of route tables and network gateways. You can also create
a hardware Virtual Private Network (VPN) connection between your corporate datacenter and your Amazon VPC, so you can use the AWS Cloud as 
an extension of your corporate datacenter.

For protecting communication between your Amazon VPC and your corporate datacenter, you can select from several VPN connectivity 
options, and choose one that best matches your needs. You can use the AWS Client VPN to enable secure access to your AWS resources 
using client-based VPN services. You can also use a third-party software VPN appliance available in the AWS Marketplace, 
which you can install on an Amazon EC2 instance in your Amazon VPC. Alternatively, you can create an IPsec VPN connection 
to protect the communication between your VPC and your remote network. To create a dedicated private connection from a 
remote network to your Amazon VPC, you can use AWS Direct Connect. You can combine this connection with an AWS Site-to-Site 
VPN to create an IPsec-encrypted private connection.

AWS provides HTTPS endpoints using the TLS protocol for communication, which provides encryption in transit when you use 
AWS APIs. TLS is a set of industry-standard cryptographic protocols used for encrypting information that is exchanged 
over the network. AES-256 is a 256-bit encryption cipher used for data transmission in TLS. 
You can use the AWS Certificate Manager (ACM) service to generate, manage, and deploy the private and public 
certificates you use to establish encrypted transport between systems for your workloads. Elastic Load Balancing is 
integrated with ACM and is used to support HTTPS protocols. If your content is distributed through Amazon CloudFront, 
it supports encrypted endpoints.

You can use Secure Socket Layer (SSL) or Transport Layer Security (TLS) from your application to encrypt a 
connection to a DB instance running MariaDB, Microsoft SQL Server, MySQL, Oracle, or PostgreSQL.

SSL/TLS connections provide one layer of security by encrypting data that moves between your client and a DB instance. 
Using a server certificate provides an extra layer of security by validating that the connection is being made to an 
Amazon RDS DB instance. It does so by checking the server certificate that is automatically installed on all DB 
instances that you provision.

