---
author: "Denis Nu\u021Biu"
categories:
- Reverse Engineering
date: '2020-02-01T14:04:27+00:00'
guid: https://nuculabs.dev/?p=770
id: 770
image: /wp-content/uploads/2020/02/68186-picoctf-logo-og.png
layout: post
permalink: /2020/02/01/picoctf-2019-reverse-engineering-vault-doors/
tags:
- picoCTF2019
- vault doors
title: picoCTF 2019 Reverse Engineering Vault Doors
---
I’m always late to the party but here’s my solutions to the PicoCTF2019 Vault Doors challenges from the reverse engineering section. I did it this mainly to improve my skills and hopefully to learn some new things.


## vault-door-training – Points: 50 


> Your mission is to enter Dr. Evil’s laboratory and retrieve the blueprints for his Doomsday Project. The laboratory is protected by a series of locked vault doors. Each door is controlled by a computer and requires a password to open. Unfortunately, our undercover agents have not been able to obtain the secret passwords for the vault doors, but one of our junior agents obtained the source code for each vault’s computer! You will need to read the source code for each level to figure out what the password is for that vault door. As a warmup, we have created a replica vault in our training facility. The source code for the training vault is here: VaultDoorTraining.java


The password is in the source code. You can copy/paste it and put it in the picoCTF{flag} format.


```
```java
    public boolean checkPassword(String password) {
        return password.equals("w4rm1ng_Up_w1tH_jAv4_3b500738c12");
    }
```
```


##  vault-door-1 – Points: 100


The second challenge is the same as the first one but you’ll have to re-order the array. Associate each character comparison with the array index and you’ll get something like:


**d35cr4mbl3\_tH3\_cH4r4cT3r5\_03f841**


##  vault-door-3 – Points: 200 


The third challenge uses loops and array in order to construct the password, we can solve it by feeding it some dummy data and see how it modifies it:


```
```java
        String s = new String(buffer);
        System.out.print("s: " + s);
        //in  jU5t_a_s01234567 89abcdefghijklmn
        //ou: jU5t_a_s76543210 m9kbidgfehcjal8n
        //    jU5t_a_sna_3lpm1 3gc49_u_4_m0rf41 
        //s:  jU5t_a_s1mpl3_an4gr4m_4_u_90cf31
        return s.equals("jU5t_a_sna_3lpm13gc49_u_4_m0rf41");
    }


    public static void main(String[] args) {
        boolean result = checkPassword("jU5t_a_s0123456789abcdefghijklmn");
        System.out.print("nDone: " + result);
    }
```
```


## vault-door-4 – Points: 250


The 4th vault door challenge is even easier, all you need to do is convert numbers from different bases back to ASCII characters. Use online number converters.


```
```java
        byte[] myBytes = {
            106 , 85  , 53  , 116 , 95  , 52  , 95  , 98  , // ascii: jU5t_4_b
            0x55, 0x6e, 0x43, 0x68, 0x5f, 0x30, 0x66, 0x5f, // hex: UnCh_0f_
            0142, 0131, 0164, 063 , 0163, 0137, 063 , 0141, // octal: bYt3s_3a
            '7' , '2' , '4' , 'c' , '8' , 'f' , '9' , '2' , // characters: 724c8f92
            // jU5t_4_bUnCh_0f_bYt3s_3a724c8f92
        };
```
```


## vault-door-5 – Points: 300


This challenge has the password URL Encoded then Base64 Encode, to solve it you just decode it. You can do it using [CyberChef](https://gchq.github.io/CyberChef/#recipe=From_Base64('A-Za-z0-9%2B/%3D',true)URL_Decode()&input=SlRZekpUTXdKVFpsSlRjMkpUTXpKVGN5SlRjMEpUTXhKVFpsSlRZM0pUVm1KVFkySlRjeUpUTXdKVFprSlRWbUpUWXlKVFl4SlRNMUpUWTFKVFZtSlRNMkpUTTBKVFZt).


```
```java
    public boolean checkPassword(String password) {
        String urlEncoded = urlEncode(password.getBytes());
        String base64Encoded = base64Encode(urlEncoded.getBytes());
        String expected = "JTYzJTMwJTZlJTc2JTMzJTcyJTc0JTMxJTZlJTY3JTVm"
                        + "JTY2JTcyJTMwJTZkJTVmJTYyJTYxJTM1JTY1JTVmJTM2"
                        + "JTM0JTVmJTMxJTMxJTM3JTM3JTY2JTM3JTM4JTMz";
        return base64Encoded.equals(expected);
    }
```
```


## vault-door-6 – Points: 350


This challenge uses a XOR encryption scheme, we can defeat it using CyberChef or modify the code and run it to spit out the password:


```
```
    public void checkPassword() {
        byte[] myBytes = {
            0x3b, 0x65, 0x21, 0xa , 0x38, 0x0 , 0x36, 0x1d,
            0xa , 0x3d, 0x61, 0x27, 0x11, 0x66, 0x27, 0xa ,
            0x21, 0x1d, 0x61, 0x3b, 0xa , 0x2d, 0x65, 0x27,
            0xa , 0x60, 0x62, 0x36, 0x67, 0x6d, 0x6c, 0x67,
        };
        for (int i=0; i<32; i++) {
            System.out.print( (char) (myBytes[i] ^ 0x55) );
        }
    }
```
```


## vault-door-7 – Points: 400


To solve this challenge I’ve used this [Binary to Decimal](https://www.mathsisfun.com/binary-decimal-hexadecimal-converter.html) converter to grab the hex values then I used CyberChef to convert them back into ASCII.


## vault-door-8 – Points: 450


The final challenge can be solved by reversing the steps of the scramble function, everything else remains unchanged. We then run it by feeding the expected bytes to the modified scramble function.


```
```java
 public char[] scramble(String i) {
  /* Scramble a password by transposing pairs of bits. */
  char[] a = i.toCharArray();
  for (int b = 0; b < a.length; b++) {
   char c = a[b];
   c = switchBits(c, 6, 7);
   c = switchBits(c, 2, 5);
   c = switchBits(c, 3, 4);
   c = switchBits(c, 0, 1);
   c = switchBits(c, 4, 7);
   c = switchBits(c, 5, 6);
   c = switchBits(c, 0, 3);
   c = switchBits(c, 1, 2);
   a[b] = c;
  }
  return a;
 }
// You'll need to pass the expected array to the scramble function for unscrambling:
  System.out.println("Expected " + String.valueOf(expected));
  System.out.println("Scrambled " + String.valueOf(scramble(String.valueOf(expected))));
```
```


That’s all! Hope you liked it.