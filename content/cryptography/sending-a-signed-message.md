---
layout: default
title:  "Sending a Signed Message"
---

# Sending a Signed Message
{:.no_toc}

* TOC
{:toc}

## Overview
Suppose Alice wants to send a message to Bob, but the message is in danger of being altered on its way. Bob wants to make sure the message received is authentic, i.e. it is not altered. 

How can this be achieved? 
- Alice _signs_ the message with her _private key_
- Bob _verifies_ the message with _Alice's certificate_

## Prerequisites
- Alice generates a key pair that contains her private key and her public key
  - The following command creates the keystore `alice.p12`
  - This keystore contains Alice 's private key and her public key
  - Password for the keystore itself is `i-am-alice`
  - Password for the private key is also `i-am-alice`

```bash
keytool -genkeypair                                 \
        -alias alice-key-pair                       \
        -keyalg RSA                                 \
        -keysize 2048                               \
        -sigalg SHA256withRSA                       \
        -keystore alice.p12                         \
        -storetype pkcs12                           \
        -storepass i-am-alice                       \
        -keypass i-am-alice                         \
        -dname "CN=CN OU=OU, O=O, L=L, ST=ON, C=CA" \
        -noprompt                                   \
        -validity 36500        
```

- Alice exports her certificate from the keystore
  - The following command creates `alice.cert`
  - Certificate contains only the public key

```bash
keytool -export               \
        -keystore alice.p12   \
        -alias alice-key-pair \
        -file alice.cert      \
        -storepass i-am-alice
```

- Alice sends her certificate to Bob
- Bob loads Alice 's certificate to his own keystore
  - Bob already has his own keystore file `bob.p12` with password `i-am-bob`
    - You can use [KeyStore Explorer](https://keystore-explorer.org/) to create an empty keystore
  - The following command loads Alice 's certificate to Bob 's keystore
    - Bob only has Alice 's certificate, he does not need to know her keystore or private key password

```bash
keytool -importcert         \
        -file alice.cert    \
        -keystore bob.p12   \
        -alias "alice-cert" \
        -storepass i-am-bob \
        -noprompt
```

## Java Application
Directory Layout
```plaintext
.
├── App.java
├── alice.cert
├── alice.p12
└── bob.p12
```
App.java
```java
class SignedMessage {
    String msg;
    byte[] sign;
}

class Alice {
    SignedMessage signedMessage() throws Exception {
        try (InputStream is = new FileInputStream("alice.p12")) {
            KeyStore ks = KeyStore.getInstance("pkcs12");
            char[] pw = "i-am-alice".toCharArray();
            ks.load(is, pw);
            Key privateKey = ks.getKey("alice-key-pair", pw);

            Signature signature = Signature.getInstance("SHA256withRSA");
            signature.initSign((PrivateKey) privateKey);

            String msg = "My Secret Message";
            signature.update(msg.getBytes(UTF_8));
            byte[] sign = signature.sign();
            
            SignedMessage signedMessage = new SignedMessage();
            signedMessage.msg = msg;
            signedMessage.sign = sign;
            return signedMessage;
        }
    }
}

/**
 * Bob has access to his keystore, where Alice 's certificate is loaded.
 * Bob does not have access to Alice 's private key or any of her passwords.
 */
class Bob {
    boolean receive(SignedMessage signedMessage) throws Exception {
        InputStream is = new FileInputStream("bob.p12");        
        KeyStore keyStore = KeyStore.getInstance("pkcs12");
        keyStore.load(is, "i-am-bob".toCharArray());

        Signature signature = Signature.getInstance("SHA256withRSA");
        signature.initVerify(keyStore.getCertificate("alice-cert"));
        signature.update(signedMessage.msg.getBytes(UTF_8));

        is.close();
        return signature.verify(signedMessage.sign);
    }
}

class App {
    public static void main(String[] args) throws Exception {
        SignedMessage signedMessage = new Alice().signedMessage();
        boolean isAuthentic = new Bob().receive(signedMessage);
        System.out.println(isAuthentic);
    }
}
```

### Sample Execution
Running the following commands will output `true` in the console since the message is not altered.

```bash
javac -d target App.java
mv *.p12 ./target/
mv *.cert ./target/
cd target
java App
```

To see an example of case where the message can not be verified, modify the main method slightly as seen below.

```java
SignedMessage signedMessage = new Alice().signedMessage();
signedMessage.msg = signedMessage.msg + "-modified";
```

Executing the modified version will print `false` since the message is altered and it can not be authenticated.
