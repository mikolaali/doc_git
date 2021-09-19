PKI 
  1. /dev/urandom 32768 > /root/.rnd
     openssl rand -rand 50 > .rnd
  1.1 dh_param
  2. /etc/ssl/openssl.cnf - option for cert
     rootKeyCert:
       openssl  req -new -x509 -days 5000 -keyform PEM -outform PEM
-subj '/CN=Organization/emailAddress=pki@domain.ru/OU=Organization/O=Organization/L=Yoshkar-Ola/ST=Mari El/C=RU'
       -key ca.key -out ca.cer

       openssl req -newkey rsa:4096 -x509
              -keyform PEM 
              -keyout ca.key 
              -days 3650 
              -outform PEM 
              -out ca.cer
       with password:
         openssl req -new -x509 -days 5000
          -subj '/CN=Organization/emailAddress=pki@domain.ru/OU=Organization/O=Organization/
             L=Yoshkar-Ola/ST=Mari El/C=RU' -keyout CA.key -out CA.crt

  3. Закрытый ключ для сервера:
       openssl genrsa -out server.key 4096
  4. csr:
       openssl req -new -key server.key -out server.req -sha256
  5. signing server cert:
       openssl x509 -req -in server.req -CA ca.cer -CAkey ca.key -set_serial 100
         -extensions server -days 1460 -outform PEM -out server.cer -sha256
  6. clients:
       key: openssl genrsa -out client.key 4096
       csr: openssl req -new -key client.key -out client.req
       key and csr: openssl req -newkey rsa:2048 -days 1000 -nodes -keyout client.key > client.req
       sign: openssl x509 -req -in client.req -days 1000 -CA ca.cer -CAkey ca.key -set_serial 01 > client.cer
       or
       openssl x509 -req -days 365 -sha256 -in client.req -CA ca.cer -CAkey ca.key 
         -CAcreateserail -out client.cer

HTTPS key and cert:
  1. key: openssl genrsa -out domain.ru.key 2048

  2. req: openssl req -new
     -subj '/CN=*.domain.ru/emailAddress=pki@domain.ru/OU=IT/O=Organization/L=Yoshkar-Ola/ST=Mari El/C=RU'\
     -out domain.ru.csr -key domain.ru.key
  3. sign: openssl x509 -req -CA CA.crt -CAkey CA.key -CAcreateserial
         -in domain.ru.csr -out domain.ru.crt -days 1000 

Harbor example
        Gen selfsigned CA cert and key
        openssl req -nodes -x509 -newkey rsa:4096 -sha512 -days 3650
        -keyout ca.key -out ca.crt
        -subj "/C=CN/ST=Beijing/L=Beijing/O=example/OU=Personal/CN=harbor"

        Create cert and csr request for harbor
        openssl req -nodes -newkey rsa:4096 -sha512 -keyout harbor.key -out harbor.csr
        -subj "/C=CN/ST=Beijing/L=Beijing/O=example/OU=Personal/CN=harbor"

KEY RSA: 
  openssl genrsa -out file.key [-des | -des3 | -idea] [-rand file] [2048|4096]
 
PUBLIC KEY:
  openssl rsa -in key [-out file] [-des | -des3 |-idea] [-check] [-pubout]

REQUEST new:
  openssl req -x509 -new -key key.pem -config cfg -out selfcert.pem -days 365
  -new(self-signed) -x509(cert) - serv|ca 

SHOW cert:
  openssl x509 -in ca.crt -text -noout  [-fingerprint -md5|sha1] [-modulus(pub key)]
   -serial | -subject | -issuer | -startdate | -enddate

EXPORT TO PKCS12: openssl pkcs12 -export -inkey client.key -in client.cer -out client.p12

PASS DGST CHANGE|DELETE: openssl rsa -in key.pem -out key1.pem -idea

TEST:
  openssl s_client

PEM -> DER: openssl rsa -inform PEM -outform DER -in priv.pem -out priv.der
PEM-->DER: openssl x509 -inform PEM -in cert.pem -outform DER -out cert.cer
DER-->PEM: openssl x509 -inform DER -in cert.cer -outform PEM -out cert.pem

CIPHERS show: openssl ciphers

ENCRYPT:
  openssl cipher -in file -out file , where cipher = base-64(преобразование в текстовый вид),
    bf(blowfish - 128 бит), des(56 бит), des3(168 бит), rc4(128 бит), rc5(128 бит), rc2 и idea(128 бит)
  
HASH: openssl hashalg file, where hashalg=[md5,sha1, ]

SIGN:
  openssl dgst -signature signature -verify pub_key file[s]
  openssl x509 -req -in clientreq.pem -extfile /usr/lib/ssl/openssl.cnf \
   -extensions(доп. парам. для серт 3й версии) /usr/lib/ssl/openssl.cnf 
   -CA CAcert.pem -CAkey serverkey.pem -CAcreateserial -out clientcert.pem

SMIME (enc/dec управлять ЭЦП и MIME заголовками писем):
 sign msg:
  подписывает(-sign) его с помощью сертификата(-signer) и секретного ключа(-inkey)
  openssl smime -sign -in mail.txt -text -from CEBKA@smtp.ru -to \
    user@mail.ru -subject "Signed message" -signer mycert.pem -inkey \
    private_key.pem | sendmail user@mail.ru
 verify msg:
  openssl smime -verify -in mail.msg -signer user.pem -out signedtext.txt
  полученный сертификат - в файл -signer(ЭЦП s/mime содержит публичный ключ!)
 enc:
  openssl smime -encrypt -in mail.txt -from CEBKA@smtp.ru -to user@mail.ru \
   -subject "Encrypted message" -des3 user.pem (cert получателя using des3) | sendmail user@mail.ru 
 decr:
  openssl smime -decrypt -in mail.msg -recip mycert.pem -inkey private_key.pem -out mail.txt

openssl.cnf
[ req ]  # Секция основных опций
distinguished_name = req_distinguished_name
prompt = no # неинтерактивный режим
[ req_distinguished_name ]
CN=RU  # Страна
ST=Ivanovskaya  # Область
L=Gadukino # Город
O=Krutie parni # Название организации
OU=Sysopka # Название отделения
CN=Your personal certificate # Имя для сертификата (
emailAddress=certificate@gaduk.ru

CA
  openssl ca -config f -in f (csr) -ss_cert f -out f -cert ca.crt -keyfile toSign -keyform pem|der -days
    -startdate -enddate -md sha1|sha256...  -batch (no quastion) -extensions v3section
  (defined ? (empty ? V3: V3) : V1) -extfile file  -subj arg /type0=val0/type1=val1/...

X509
  -in -out -md5 -sha1 
  display opt: -text -noout -modulus -pubkey -serial -hash -subject -serial
    -issuer -startdate -enddate -dates -fingerprint
  signing opts: -signkey f -crlext -passin arg -days n -req -set_serial n 
    -CA f  -CAkey f -CAserial f -CAcreateserial -extfile f -extensions section 
    -force_pubkey key

DHPARAM
  -inform -in -out f -dsaparam -rand f -noout -text -engine id

REQ
  -in -out -text -subject -modulus -verify -new(csr) -rand -key -keyout -nodes
  -config f -subj arg -x509 -days n  -set_serial n -extensions section -batch 
  -verbose -engine id

RSA
  -in/outform -in f  -out f -passout pass -aes128|256|-camellia128|256|-des|-des3|-idea
  -text -noout -modulus -check -pubin -pubout

RSAUTL (sign,verify,enc,dec)
  -in -out f -inkey f -pubin -certin -sign -verify -encrypt -decrypt
  -pkcs -ssl -raw -hexdump -asn1parse
         openssl rsautl -sign -in file -inkey key.pem -out sig
         openssl rsautl -verify -in sig -inkey key.pem
         openssl rsautl -verify -in sig -inkey key.pem -raw -hexdump
         openssl asn1parse -in pca-cert.pem
         openssl asn1parse -in pca-cert.pem -out sig -noout -strparse 614
         openssl x509 -in test/testx509.pem -pubkey -noout >pubkey.pem
         openssl rsautl -in sig -verify -asn1parse -inkey pubkey.pem -pubin (signature analize)
         openssl asn1parse -in pca-cert.pem -out tbs -noout -strparse 4
         openssl md5 -c tbs

DSA
  -in -out [gendsa] -aes128|256|-camellia128|256|-des|-des3|-idea
  -text -noout -modulus -pubout

ENC
  -in -out -pass -salt -nosalt -e(enc) -d(decrypt) -a(b64) -k pass -nosalt -salt
  openssl des3 -d -salt -in file.des3 -out file.txt -k mypassword
  openssl bf -a -salt -in file.txt -out file.bf

ERR
  openssl errstr errCode

CRL
  -gencrl -> create CRL based on index file
  -revoke f ( f=cert to revoke => index.txt )
  -status serial
  -in -out f | -inform|-outform DER/PEM -text -noout -issuer -nextupdate -CAfile -CApath

CONFIGURATION
  default_md|database|serial|policy

s_client (dial tool)
  -connect host:port
  -servername name -key
