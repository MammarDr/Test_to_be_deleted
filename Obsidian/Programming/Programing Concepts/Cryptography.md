**Cryptography** is a method of protecting information and communications through the use of codes, so that only those for whom the information is intended can read and process it.

In computer science, **cryptography** refers to secure information and communication techniques derived from mathematical concepts and a set of rule-based calculations called algorithms, to transform messages in ways that are hard to decipher. These deterministic algorithms are used for cryptographic key generation, digital signing, verification to protect data privacy, web browsing on the internet and confidential communications such as credit card transactions and email.

**Cryptography** is the practice and study of techniques for secure communication in the presence of third parties. In C#, you can use the `System.Security.Cryptography` namespace to work with cryptographic functions. Here's a brief overview of some commonly used cryptographic operations in C# such as:

- Hashing
- Symmetric Encryption
- Asymmetric Encryption (Public Key Cryptography)

## Hashing:

Hash functions are commonly used to create a fixed-size string of characters, which is typically a hash value, from variable-size input data.

Hash functions, by design, are one-way functions. This means that you cannot directly reverse a hash to retrieve the original data. Hashing is primarily used for integrity verification and not for data retrieval.

If you need to check whether a given input matches a previously computed hash, you can hash the new input and compare the resulting hash with the stored hash. If the hashes match, it suggests that the input data is the same.

**SHA-256** (Secure Hash Algorithm 256-bit) is a cryptographic hash function that belongs to the SHA-2 family of hash functions. It's one of the widely used hash functions for various security applications and protocols. The "256" in SHA-256 refers to the bit length of the hash output, which is 256 bits.

Common use cases for SHA-256 include:

- Password Hashing: Storing hashed passwords rather than plain text in databases for security.
- Digital Signatures: Creating digital signatures for documents and messages to ensure authenticity and integrity.
- Blockchain Technology: SHA-256 is used in many blockchain protocols (e.g., Bitcoin) to secure transactions and blocks.
- Data Integrity: Verifying the integrity of transmitted or stored data by comparing hash values.

To use SHA-256 in programming, you typically leverage libraries or classes provided by the programming language. In C#, for example, you can use the `System.Security.Cryptography` namespace, as demonstrated in the code examples provided in previous responses.


```csharp
static public string ComputeHash(string data)
{
    using(SHA256 sh256 = SHA256.Create())
    {
        byte[] buffer = Encoding.UTF8.GetBytes(data);
//      64Char : Hex = 4byte 
        byte[] hashByte = sh256.ComputeHash(buffer);
        
// "3B-F8-9E-2E-EB-8F-50-E8-1C-2F-43-9C-EA-3B-4B-A4-D4-08-3F-9A-FF-8E-CC-D7-73-B5-9B"
        string UnFormatedHash = BitConverter.ToString(hashByte);
        
// "3BF89E2EEB8F50E81C2F439CEA3B4BA4D4083F9AFF8ECCD773B59B"
        string FormatedHash = UnFormatedHash.Replace("-", "");
        
// "3bf89e2eeb8f50e81c2f439cea3b4ba4d4083f9aff8eccd773b59b"
        string hashCode = FormatedHash.ToLower();

        return hashCode;
    }
}


string data = "wish i will stop it";

string hashData = ComputeHash(data);

```


## Symmetric Encryption

**Symmetric encryption** is a type of encryption where the same key is used for both the encryption and decryption of the data. In C#, the .NET Framework and .NET Core provide classes in the `System.Security.Cryptography` namespace to perform symmetric encryption. The most commonly used symmetric encryption algorithms are DES (Data Encryption Standard), 3DES (Triple DES), AES (Advanced Encryption Standard), and RC4.

  

#### **They key size is commonly used for AES encryption is 128-bit, what does that mean?**

The "key size" in the context of **AES (Advanced Encryption Standard)** refers to the length of the cryptographic key used for encryption and decryption. In AES, the key size determines the number of bits in the key, and it directly affects the strength of the encryption.

AES supports three key sizes: 128-bit, 192-bit, and 256-bit. The number in each key size (128, 192, or 256) represents the length of the key in bits. Here's a breakdown of what each of these key sizes means:

- 128-bit Key:
    - The key is 128 bits long, which means it has 2^128 possible combinations.
    - This is considered strong encryption and is widely used for many secure applications.
- 192-bit Key:
    - The key is 192 bits long, providing a higher level of security than 128-bit keys.
    - While it offers increased security, 192-bit keys are less commonly used than 128-bit and 256-bit keys.
- 256-bit Key:
    - The key is 256 bits long, providing the highest level of security among the three key sizes.
    - AES with a 256-bit key is often used in situations where maximum security is required.

The key size directly impacts the difficulty of a brute-force attack, where an attacker tries all possible combinations of the key to decrypt the data. As the key size increases, the number of possible combinations grows exponentially, making it significantly more difficult and time-consuming for an attacker to break the encryption through brute force.

In practice, a 128-bit key is considered secure for most applications, and it is widely used in various cryptographic protocols and systems. However, for scenarios where an extra layer of security is desired or required, one might opt for a 192-bit or 256-bit key. It's important to note that the increased security comes with a trade-off in terms of computational overhead, as encryption and decryption with longer keys can be more computationally intensive.

**You should not lose the key!**

Remember that in real-world scenarios, you would typically use a more secure method for key management, and for sensitive information, it's important to handle encryption keys securely. Additionally, consider using authenticated encryption modes for added security.

```csharp
class Program
{
    static void Main()
    {
        // Original data
        string originalData = "Sensitive information";


        // Key for AES encryption (128-bit key)
        string key = "1234567890123456";


        // Encrypt the original data
        string encryptedData = Encrypt(originalData, key);


        // Decrypt the encrypted data
        string decryptedData = Decrypt(encryptedData, key);


        // Display results
        Console.WriteLine($"Original Data: {originalData}");
        Console.WriteLine($"Encrypted Data: {encryptedData}");
        Console.WriteLine($"Decrypted Data: {decryptedData}");
    }


    static string Encrypt(string plainText, string key)
    {
        using (Aes aesAlg = Aes.Create())
        {
            // Set the key and IV for AES encryption
            aesAlg.Key = Encoding.UTF8.GetBytes(key);
            aesAlg.IV = new byte[aesAlg.BlockSize / 8];


            // Create an encryptor
            ICryptoTransform encryptor = aesAlg.CreateEncryptor(aesAlg.Key, aesAlg.IV);


            // Encrypt the data
            using (var msEncrypt = new System.IO.MemoryStream())
            {
                using (var csEncrypt = new CryptoStream(msEncrypt, encryptor, CryptoStreamMode.Write))
                using (var swEncrypt = new System.IO.StreamWriter(csEncrypt))
                {
                    swEncrypt.Write(plainText);
                }


                // Return the encrypted data as a Base64-encoded string
                return Convert.ToBase64String(msEncrypt.ToArray());
            }
        }
    }


    static string Decrypt(string cipherText, string key)
    {
        using (Aes aesAlg = Aes.Create())
        {
            // Set the key and IV for AES decryption
            aesAlg.Key = Encoding.UTF8.GetBytes(key);
            aesAlg.IV = new byte[aesAlg.BlockSize / 8];


            // Create a decryptor
            ICryptoTransform decryptor = aesAlg.CreateDecryptor(aesAlg.Key, aesAlg.IV);


            // Decrypt the data
            using (var msDecrypt = new System.IO.MemoryStream(Convert.FromBase64String(cipherText)))
            using (var csDecrypt = new CryptoStream(msDecrypt, decryptor, CryptoStreamMode.Read))
            using (var srDecrypt = new System.IO.StreamReader(csDecrypt))
            {
                // Read the decrypted data from the StreamReader
                return srDecrypt.ReadToEnd();
            }
        }
    }
}
```


##  Asymmetric Encryption

Asymmetric encryption, also known as public-key cryptography, is a cryptographic system that uses pairs of keys: public keys, which are widely shared or distributed, and private keys, which are kept secret. This system enables two parties to secure their communication over an insecure channel without having to share a secret key beforehand.

Here's a brief overview of how asymmetric encryption works:

- Key Pairs:
    - Public Key: This key is made available to anyone and can be freely distributed. It's commonly used for encryption.
    - Private Key: This key is kept secret and is known only to the owner. It's used for decryption.
- Encryption and Decryption:
    - If Party A wants to send an encrypted message to Party B, Party A uses Party B's public key to encrypt the message.
    - Only Party B, who possesses the corresponding private key, can decrypt and read the message.
- Digital Signatures:
    - The roles can be reversed for digital signatures. If Party A wants to sign a message and prove its authenticity, Party A uses its private key to create a digital signature.
    - Anyone with access to Party A's public key can verify that the signature is valid, confirming that the message has not been tampered with and is indeed from Party A.
- Key Distribution:
    - Asymmetric encryption helps solve the key distribution problem inherent in symmetric key cryptography. In a symmetric key system, both parties need to have the same secret key, which can be challenging to distribute securely.
    - With asymmetric encryption, each participant has their own pair of public and private keys.

Popular algorithms used in asymmetric encryption include RSA (Rivest-Shamir-Adleman), ECC (Elliptic Curve Cryptography), and DSA (Digital Signature Algorithm). Asymmetric encryption is often used in combination with symmetric encryption for efficiency and security in various cryptographic protocols and applications, such as secure communication over the internet, digital signatures, and key exchange in secure protocols like SSL/TLS.

  

Why two Keys?

The use of two keys (public and private keys) in asymmetric encryption serves several important purposes:

1. Encryption and Decryption:

- - Public Key: Used for encryption. Anyone can use the public key to encrypt a message.
    - Private Key: Used for decryption. Only the owner of the private key can decrypt messages encrypted with the corresponding public key.
- This separation of roles allows for secure communication between parties without the need for them to share a secret key for encryption and decryption.

1. Digital Signatures:

- - Private Key: Used to create a digital signature, providing a way for the owner of the private key to authenticate messages.
    - Public Key: Used to verify the digital signature. Anyone with access to the public key can verify that the message was signed by the corresponding private key.

1. Digital signatures help ensure the integrity and authenticity of messages, allowing the recipient to verify that a message has not been tampered with and that it indeed comes from the claimed sender.
2. Key Distribution:

- - Public Keys: Can be freely distributed and shared. For example, in secure communication, users can publish their public keys on a public key server or share them directly with others.
    - Private Keys: Kept secret by the key owner. They do not need to be shared with others.

1. This separation simplifies the key distribution problem that exists in symmetric key cryptography, where both parties need to have the same secret key. With asymmetric encryption, each user has their own pair of keys, and they only need to share their public keys.

The combination of public and private keys in asymmetric encryption provides a flexible and secure framework for various cryptographic applications, including secure communication, digital signatures, and key exchange.

```csharp
 try
        {
            // Generate public and private key pair
            using (RSACryptoServiceProvider rsa = new RSACryptoServiceProvider())
            {
                // Get the public key
                /*
                 When exporting the public key, ToXmlString(false) is used with the argument set 
                 to false to indicate that only the public parameters should be included in the XML string.
                 */
                string publicKey = rsa.ToXmlString(false);


                // Get the private key
                string privateKey = rsa.ToXmlString(true);


                // Original message
                string originalMessage = "Hello, this is a secret message!";


                // Encrypt using the public key
                string encryptedMessage = Encrypt(originalMessage, publicKey);


                // Decrypt using the private key
                string decryptedMessage = Decrypt(encryptedMessage, privateKey);


                // Display the results
                Console.WriteLine($"\n\nPublic Key:\n {publicKey}");
                Console.WriteLine($"\n\nPrivate Key:\n {privateKey}");
                Console.WriteLine($"\nOriginal Message:\n {originalMessage}");
                Console.WriteLine($"\nEncrypted Message:\n {encryptedMessage}");
                Console.WriteLine($"\nDecrypted Message:\n {decryptedMessage}");


                // Wait for user input before closing the console window
                Console.WriteLine("\nPress any key to exit...");
                Console.ReadKey();
            }
        }
        catch (CryptographicException ex)
        {
            Console.WriteLine($"Encryption/Decryption error: {ex.Message}");
            Console.ReadKey();
        }
        catch (Exception ex)
        {
            Console.WriteLine($"An unexpected error occurred: {ex.Message}");
            Console.ReadKey();
        }
    }


    static string Encrypt(string plainText, string publicKey)
    {
        try
        {
            using (RSACryptoServiceProvider rsa = new RSACryptoServiceProvider())
            {
                rsa.FromXmlString(publicKey);


                byte[] encryptedData = rsa.Encrypt(Encoding.UTF8.GetBytes(plainText), false);
                return Convert.ToBase64String(encryptedData);
            }
        }
        catch (CryptographicException ex)
        {
            Console.WriteLine($"Encryption error: {ex.Message}");
            throw; // Rethrow the exception to be caught in the Main method
        }
    }


    static string Decrypt(string cipherText, string privateKey)
    {
        try
        {
            using (RSACryptoServiceProvider rsa = new RSACryptoServiceProvider())
            {
                rsa.FromXmlString(privateKey);


                byte[] encryptedData = Convert.FromBase64String(cipherText);
                byte[] decryptedData = rsa.Decrypt(encryptedData, false);


                return Encoding.UTF8.GetString(decryptedData);
            }
        }
        catch (CryptographicException ex)
        {
            Console.WriteLine($"Decryption error: {ex.Message}");
            throw; // Rethrow the exception to be caught in the Main method
        }
    }
}
```