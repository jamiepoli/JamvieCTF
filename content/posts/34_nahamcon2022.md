---
title: "NahamCon CTF 2022"
date: 2022-05-02T12:40:52-07:00
draft: false
comments: false
images:
categories:
  - writeups
tags: 
  - writeups
  - web
  - sqli
  - ssti
  - csrf
  - arduino

toc: true
---

## Just in Time

Maple Bacon played in NahamCon CTF 2022 this past weekend, which came as a surprise - we were fully planning on focusing our efforts onto AngstromCTF which overlapped, however, NahamCon CTF proved to be an interesting and good-quality so a last-minute decision was made to participate in both. AngstromCTF is still underway, but in the awkward passage of time between my last week and weekend, I played in NahamCon CTF for a little bit of practice. 

## Flaskmetal Alchemist

Snoop around for a little bit and observe that the sqlalchemy libary version the app is using is 0.2, which is incredibly old. Google "sqlalchemy/0.2" specifically and you'll recieve a few [hits](https://security.snyk.io/vuln/SNYK-PYTHON-SQLALCHEMY-173678) on a potential SQLi vulnerability in the group_by/order_by function. Luckily for us, the application indeed utilizes the `order_by` function. 

```js
            metals = Metal.query.filter(
                Metal.name.like("%{}%".format(search))
            ).order_by(text(order))
```

Simply create an `ORDER BY`-based blind SQLi that changes the order of the elements as a boolean condition. 

```
search=Li&order=(CASE WHEN (SELECT HEX(SUBSTR(flag, 1, 1)) FROM flag)="66" THEN atomic_number ELSE name END)
```

Repeat this in a script and grab the flag character by character. 

`flag{order_By_blind}`

## Two For One 

The challenge is a pastebin-esque site but with added otp functionality for basically everything - from viewing and deleting notes, to changing your password.

With no source provided, snoop around and observe a feedback form in the settings tab. Play around with it and observe that it is vulnerable to XSS, whatever is submitted in the feedback form is rendered to an "admin" user. 

We need 2 things: the admin's 2fa secret key and password. Start with the 2fa, since you need it for the password anyway. 

In the settings page, observe a "reset 2fa" button which the server responds with an `otpauth` containing your username and a 2fa secret key. 

```
otpauth://totp/Fort%20Knox:<USER>?secret=<BUNCHOFLETTERSANDNUMBERS>&issuer=Fort%20Knox
```

Use the XSS functionality in the feedback form to send the admin to that endpoint and observe the 2fa secret key value that is returned - you now have the admin's 2fa (sidenote - it's good to have a [totp generator](https://totp.danhersam.com/) handy for this occassion).

{{< image src="/images/nahamcon2022_twoforone_2fa.png" alt="2fa gone bad" position="center" style="border-radius: 8px;" >}}

The next step is to retrieve their password, which you can do by again using the feedback form to send the admin to make a POST request to the `change_password` endpoint. The request will need a valid 2fa value alongside the new password, which you should have from before. Once you succesfully reset the admin's password, log into their account yourself and view their secret note. 

{{< image src="/images/nahamcon2022_twoforone_flag.png" alt="2fa gone bad" position="center" style="border-radius: 8px;" >}}

## Deafcon 

The challenge asks for your name and email to be rendered onto a fake "deaf con" ticket as a PDF.

Observe that the ticket service has an alphanumeric whitelist on the name value, but does not have as strong of a restriction on the email value - only `()` chars are banned and the email must be RFC-compliant, which is easy to do by adding `@any.thing` at the end.

Script execution is possible, in a similar fashion to dangling markup. If you add an iframe or an img, the PDF will render it (albeit malformed). However, the service is also vulnerable to SSTI attacks, which can be tested by adding in a trivial payload such as `{{7*7}}` into the email field. 

Craft an SSTI payload to find any file named `flag`. 

## Dweeno

You're given a few things: `output.txt` which is a list of binary values, a `source.ino` file, an image of an arduino hooked up to a breadboard with a chip in the middle, and a sketch of the arduino as well. 

{{< image src="/images/nahamcon2022_arduinosketch.png" alt="arduino" position="center" style="border-radius: 8px;" >}}

Observe the sketch and note that the chip in the breadboard is a quad XOR 2-gate, hooked up to the input and output pins of the arduino. Observe the `source.ino` file: 

```
char * flag = "REDACTED";
String curr, first, second;
int in1=29, in2=27, in3=25, in4=23;
int out1=53, out2=51, out3=49, out4=47;
int i;

String get_output(String bits) {
    String output;
    digitalWrite(out1, ((bits[0] == '1')? HIGH : LOW));
    digitalWrite(out2, ((bits[1] == '1')? HIGH : LOW));
    digitalWrite(out3, ((bits[2] == '1')? HIGH : LOW));
    digitalWrite(out4, ((bits[3] == '1')? HIGH : LOW));
    delay(1000);
    output += String(digitalRead(in1));
    output += String(digitalRead(in2));
    output += String(digitalRead(in3));
    output += String(digitalRead(in4));
    return output;
}

//converts a given number into binary
String binary(int number) {
  String r;
  while(number!=0) {
    r = (number % 2 == 0 ? "0" : "1")+r; 
    number /= 2;
  }
  while ((int) r.length() < 8) {
    r = "0"+r;
  }
  return r;
}

void setup() {
  i = 0;
  pinMode(out1, OUTPUT);
  pinMode(out2, OUTPUT);
  pinMode(out3, OUTPUT);
  pinMode(out4, OUTPUT);
  pinMode(in1, INPUT);
  pinMode(in2, INPUT);
  pinMode(in3, INPUT);
  pinMode(in4, INPUT);
  Serial.begin(9600);
}

void loop() {
  if (i < strlen(flag)) {
    curr = binary(flag[i]);
    first = curr.substring(0,4);
    second = curr.substring(4,8);
    Serial.print(get_output(first));
    Serial.println(get_output(second));
    delay(1000);
    i++;
  }
}
```

Of note in the code is the `loop()` function and `get_output()` function. The `loop()` iterates through each character in the global `flag` variable, then splits it in half and feeds both halves to `get_output()`.

The `get_output` function first calls to the arduino hardware and sets the 4 output pins to either a high voltage (HIGH) or low voltage (LOW) based on the value of a specific bit recieved in the input. Basically, it sets the output pins to either a 0 or 1 based on one of the bits in the input. 
After a short delay, the function then reads the value from the input pins, which, recalling the sketch of the arduino, would have been hooked up with the output pins to a quad XOR gate. THerefore, the input pins would return either a 0 or 1 depending on the XOR operation. 

To summarize, the functionality of dweeno is to simply XOR the bits of the flag sequentially. We still need to figure out what the output pins are being XOR'ed with to get the value of the input pins - however, we know that the output.txt spits out the XOR'ed flag. We can move backwards by determining that the first binary value in the output.txt file is the XOR'ed character `f`. XOR'ing the binary value of `f` with the output.txt values will return to us the key used to XOR the output pins - this is revealed to be `01010101`. Through reverse-engineering this small program, we can perform the necessary XOR operations to retrieve the flag with a simple script: 

```py
xor_boi = "01010101"
flag = ''

binary_flag = []

with open('output.txt', 'r') as fp:
	output = fp.readline()
	while output:
		curr = ''
		for i in range(0,8):
			y = int(output[i],2) ^ int(xor_boi[i],2)
			curr += str(y)
		ascii_char = chr(int(curr,2))
		flag += ascii_char
		output = fp.readline()


print(flag)

```