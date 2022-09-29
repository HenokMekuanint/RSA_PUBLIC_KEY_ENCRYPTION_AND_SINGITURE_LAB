# RSA_PUBLIC_KEY_ENCRYPTION_AND_SINGITURE_LAB

            Table of Contents 

            Overview
            
            Task 1: Deriving the Private Key

            Task 2: Encrypting a Message

            Task 3: Decrypting a Message

            Task 4: Signing a Message

            Task 5: Verifying a Signature

            Task 6: Manually Verifying an X.509 Certificate

            Conclusion
            
            
            1.Overview
            
            
RSA encryption is a cryptosystem that uses Asymmetric encryption technique that uses two different keys as public and private keys to perfrom the encryption and decryption. Asymmetric encryption is a type of encrption that uses different keys for encrypting and decrypting. Sice RSA encryption uses Public and private key for dencyrtiona and encryption its cataorized as asymmentric encryption.
The learning objective of this lab is to gain hands on experince on the RSA algorithm. The lab covers from generation of pulic and private keys to how to perform encrytion /decryption and signiture lab. C prigramming language will be used for the follwoing lab.
The above topics will be covered during the lab:

                                      Public-key cryptography
                                      The RSA algorithm and Key generation
                                      BIG number calculation
                                      Encryption and Decryption uing RSA
                                      Digital signiture
                                      X.509 certeficate

        2.Background

The RSA algorithm involves computations on large numbers. These computations cannot be directly conducted using simple arithmetic operators in programs, because those operators can only operate on primitive data types, such as 32-bit integer and 64-bit long integer types. The numbers involved in the RSA algorithms are typically more than 512 bits long. For example, to multiple two 32-bit integer numbers a and b, we just need to use a*b in our program. However, if they are big numbers, we cannot do that any more; instead, we need to use an algorithm (i.e., a function) to compute their products. There are several libraries that can perform arithmetic operations on integers of arbitrary size. In this lab, we will use the Big Number library provided by openssl. To use this library, we will define each big number as a BIGNUM type, and then use the APIs provided by the library for various operations, such as addition, multiplication, exponentiation, modular operations, etc

2.1 BIGNUM APIs 


All the big number APIs can be found from https://linux.die.net/man/3/bn. In the following, we describe some of the APIs that are needed for this lab. 

• Some of the library functions requires temporary variables. Since dynamic memory allocation to create BIGNUMs is quite expensive when used in conjunction with repeated subroutine calls, a BN CTX structure is created to holds BIGNUM temporary variables used by library functions. We need to create such a structure, and pass it to the functions that requires it.
                                      BN_CTX *ctx = BN_CTX_new()
                                      • Initialize a BIGNUM variable
                                      BIGNUM *a = BN_new()
                                      • There are a number of ways to assign a value to a BIGNUM variable.
                                      // Assign a value from a decimal number string 
                                      BN_dec2bn(&a, "12345678901112231223")

                                      // Assign a value from a hex number string 
                                      BN_hex2bn(&a, "2A3B4C55FF77889AED3F")

                                      // Generate a random number of 128 bits 
                                      BN_rand(a, 128, 0, 0);

                                      // Generate a random prime number of 128 bits 
                                      BN_generate_prime_ex(a, 128, 1, NULL, NULL, NULL);

                                      • Print out a big number.
                                      void printBN(char *msg, BIGNUM * a) {

                                      // Convert the BIGNUM to number string 
                                      char * number_str = BN_bn2dec(a); 
                                      // Print out the number string
                                      printf("%s %s\n", msg, number_str); 
                                      // Free the dynamically allocated memory OPENSSL_free(number_str);
                                      
                                      }
                                      • Compute res = a − b and res = a + b:
                                      BN_sub(res, a, b); 
                                      BN_add(res, a, b);
                                      • Compute res = a ∗ b. It should be noted that a BN CTX structure is need in this API
                                      BN_mul(res, a, b, ctx)
                                      • Compute res = a ∗ b mod n:
                                      BN_mod_mul(res, a, b, n, ctx)
                                      • Compute res = a c mod n:
                                      BN_mod_exp(res, a, c, n, ctx)
                                      • Compute modular inverse, i.e., given a, find b, such that a ∗ b mod n = 1. The value b is called the inverse of a, with respect to modular n.
                                      BN_mod_inverse(b, a, n, ctx);
                                      2.2 A Complete Example
                                      We show a complete example in the following. In this example, we initialize three BIGNUM variables, a, b, and n; we then compute a ∗ b and (a b mod n).
                                      
    ![Picture1](https://user-images.githubusercontent.com/90408697/192744666-db1ea347-9922-4650-b562-259bd44123b6.png)
                                      
                                      3.1 Task 1: Deriving the Private Key
                                      
Let p, q, and e be three prime numbers. Let n = p*q. We will use (e, n) as the public key. Please calculate the private key d. The hexadecimal values of p, q, and e are listed in the following. It should be noted that although p and q used in this task are quite large numbers, they are not large enough to be secure. We intentionally make them small for the sake of simplicity. In practice, these numbers should be at least 512 bits long (the one used here are only 128 bits).

![Picture2](https://user-images.githubusercontent.com/90408697/192744720-0fc2fba7-44a0-4e3a-8c19-038af0488625.png)

                                    3.2 Task 2: Encrypting a Message

                                    Let (e, n) be the public key. Please encrypt the message "A top secret!" (the quotations are not included). We need to convert this ASCII string to a hex string, and then convert the hex string to a BIGNUM using the hex-to-bn API BN hex2bn(). The following python command can be used to convert a plain ASCII string to a hex string.

                                    $ python -c ‘print("A top secret!".encode("hex"))’
                                    4120746f702073656372657421

                                    The public keys are listed in the followings (hexadecimal). We also provide the private key d to help you verify your encryption result. 

                                    n = DCBFFE3E51F62E09CE7032E2677A78946A849DC4CDDE3A4D0CB81629242FB1A5 
                                    e = 010001 (this hex value equals to decimal 65537) 
                                    M = A top secret! 
                                    
                                    d = 74D806F9F3A62BAE331FFE3F0A68AFE35B3D2E4794148AACBC26AA381CD7D30D
                                    
   ![Picture3](https://user-images.githubusercontent.com/90408697/192744762-f8d0c3d7-f24e-4fc3-b438-3f09a51c6a45.png)
   
                                   3.3 Task 3: Decrypting a Message
   
The public/private keys used in this task are the same as the ones used in Task 2. Please decrypt the following ciphertext C, and convert it back to a plain ASCII string.
                        C = 8C0F971DF2F3672B28811407E2DABBE1DA0FEBBBDFC7DCB67396567EA1E2493F
                        
    ![Picture4](https://user-images.githubusercontent.com/90408697/192744793-e7035dce-d820-48f8-bd1f-e5d8d23916d1.png)


                          $ python -c ’print("50617373776F72642069732064656573".decode("hex"))’
                          
                          Password is dees
                          
   ![Picture5](https://user-images.githubusercontent.com/90408697/192744813-ea0c95d4-612b-42ce-b64c-bec88c08afd2.png)
              
                                      3.4 Task 4: Signing a Message
        
   The public/private keys used in this task are the same as the ones used in Task 2. Please generate a signature for the following message (please directly sign this message, instead of signing its hash value):

                                            M1 = I owe you $2000. 
                                            M2 = I owe you $3000. 
                                            
                                            Please make a slight change to the message M, such as changing $2000 to $3000, and sign the modified message. Compare both signatures and describe what you observe.
                                            
   ![Picture6](https://user-images.githubusercontent.com/90408697/192744823-4006fdee-462a-47fa-b532-2bf95b2b0e3d.png)
         
                                     The hex value for both message is the same. 
                                     
                                     The hex value for message 1 is 49206f776520796f75202433303030
                                     
                                      The hex value for message 2 is 49206f776520796f75202433303030
                                      
                                      Signiture of m1 
                                      
                                    80A55421D72345AC199836F60D51DC9594E2BDB4AE20C804823FB71660DE7B82

                                    Signiture of m2

                                    04FC9C53ED7BBE4ED4BE2C24B0BDF7184B96290B4ED4E3959F58E94B1ECEA2E
                                    
                                    3.5 Task 5: Verifying a Signature

                                    Bob receives a message M = "Launch a missile." from Alice, with her signature S. We know that Alice’s public key is (e, n). Please verify whether the signature is indeed Alice’s or not. The public key and signature (hexadecimal) are listed in the following:

                                    M = Launch a missile.
                                    S = 643D6F34902D9C7EC90CB0B2BCA36C47FA37165C0005CAB026C0542CBDB6802F 
                                    e = 010001 (this hex value equals to decimal 65537) 
                                    n = AE1CD4DC432798D933779FBD46C6E1247F0CF1233595113AA51B450F18116115

![Picture7](https://user-images.githubusercontent.com/90408697/192744871-f3dc7d37-dd1a-48ef-ae85-d47e291c449a.png)


                                    3.6 Task 6: Manually Verifying an X.509 Certificate

In this task, we will manually verify an X.509 certificate using our program. An X.509 contains data about a public key and an issuer’s signature on the data. We will download a real X.509 certificate from a web server, get its issuer’s public key, and then use this public key to verify the signature on the certificate.


                                    Step 1: Download a certificate from a real web server. We use the www.example.org server in this document. Students should choose a different web server that has a different certificate than the one used in this document. 

                                    openssl s_client -connect www.twitter.com:443 -showcerts
                                    
                                    The Certeficate we are going to use will be www.twitter.com websited certeficate.
                                    
![Picture8](https://user-images.githubusercontent.com/90408697/192744897-04e056c7-fdfa-4b4f-bd36-97322755cad2.png)
![Picture9](https://user-images.githubusercontent.com/90408697/192744919-a55b8d1f-e261-4565-bbae-4d73e54b58a7.png)
 ![Picture10](https://user-images.githubusercontent.com/90408697/192744968-c5aac3e9-0231-4271-a9c8-aba6a0ed03f9.png)
![Picture11](https://user-images.githubusercontent.com/90408697/192744981-74089b37-c427-4b03-acde-4c910342b998.png)
            
            Step2: Extract the public key (e,n) issuer certeficate
            
![Picture13](https://user-images.githubusercontent.com/90408697/192745002-427ecc79-fe0f-4468-9b99-de492d979b1e.png)

                                    
                       Step3: Extracting The exponent
           
           
 ![Picture14](https://user-images.githubusercontent.com/90408697/192745007-3544249a-145c-455c-b4f8-ebd6178e9984.png)
![Picture15](https://user-images.githubusercontent.com/90408697/192745026-0a207bb4-50a9-4596-b7c0-dfb084d09456.png)
![Picture16](https://user-images.githubusercontent.com/90408697/192745032-f0e579ca-57d0-43b6-9b22-0b966b453d35.png)
            
            Step4: extracting the hash value
            
 ![Picture17](https://user-images.githubusercontent.com/90408697/192745036-e67c4357-056c-4fc4-a87f-f37b4ce8a3b0.png)
  ![Picture18](https://user-images.githubusercontent.com/90408697/192745061-6cb280cb-031a-40a0-9d5a-92396e6d9747.png)
  ![Picture19](https://user-images.githubusercontent.com/90408697/192745077-39c483e9-24d1-4501-a57c-e7fe98deb11e.png)








