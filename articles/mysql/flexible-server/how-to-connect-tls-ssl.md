---
title: MySQL için Azure veritabanı 'nda TLS/SSL kullanılarak şifrelenmiş bağlantı-esnek sunucu
description: MySQL için Azure veritabanı 'nda TLS/SSL kullanarak bağlanma ile ilgili yönergeler ve bilgiler-esnek sunucu.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 09/21/2020
ms.openlocfilehash: ce6150cf404f1ca68c93285a2f4a29a6373a55c0
ms.sourcegitcommit: bed20f85722deec33050e0d8881e465f94c79ac2
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/25/2021
ms.locfileid: "105110033"
---
# <a name="connect-to-azure-database-for-mysql---flexible-server-over-tls12ssl"></a>MySQL için Azure veritabanı 'na bağlanma-TLS 1.2/SSL üzerinden esnek sunucu

> [!IMPORTANT]
> MySQL için Azure veritabanı esnek sunucu şu anda genel önizlemede

MySQL için Azure veritabanı esnek sunucu, istemci uygulamalarınızı, daha önce Güvenli Yuva Katmanı (SSL) olarak bilinen Aktarım Katmanı Güvenliği (TLS) kullanarak MySQL hizmetine bağlamayı destekler. TLS, veritabanı sunucunuz ile istemci uygulamalarınız arasında şifrelenmiş ağ bağlantıları sağlayan ve uyumluluk gereksinimlerine bağlı olmanızı sağlayan bir endüstri standardı protokolüdür.

MySQL için Azure veritabanı esnek sunucu, yalnızca Aktarım Katmanı Güvenliği (TLS 1,2) kullanılarak şifrelenmiş bağlantıları destekler ve TLS 1,0 ve TLS 1,1 tüm gelen bağlantıları reddedilir. Tüm esnek sunucular için TLS bağlantıları zorlaması etkinleştirilmiştir ve esnek sunucuya bağlanmak için TLS/SSL 'yi devre dışı bırakayükleyemezsiniz.

## <a name="download-the-public-ssl-certificate"></a>Ortak SSL sertifikasını indirin
Appliations ile kullanmak için lütfen [genel SSL sertifikasını](https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem)indirin.

Sertifika dosyasını tercih ettiğiniz konuma kaydedin. Örneğin, bu öğretici `c:\ssl` `\var\www\html\bin` Yerel ortamınızda veya uygulamanızın barındırıldığı istemci ortamında kullanılır. Bu, uygulamaların SSL üzerinden veritabanına güvenli bir şekilde bağlanmasına imkan tanır. 

### <a name="connect-using-mysql-command-line-client-with-tlsssl"></a>MySQL komut satırı istemcisini TLS/SSL ile bağlama

Esnek sunucunuzu *özel erişim (VNET tümleştirmesi)* ile oluşturduysanız, sunucunuza aynı VNET içindeki bir kaynaktan sunucunuza bağlanmanız gerekir. Bir sanal makine oluşturabilir ve bunu esnek sunucunuz ile oluşturulan VNet 'e ekleyebilirsiniz.

Esnek sunucunuzu *ortak erişim (izin VERILEN IP adresleri)* ile oluşturduysanız, sunucunuzdaki güvenlik duvarı kuralları lıstesıne yerel IP adresinizi ekleyebilirsiniz.

Yerel ortamınızdan sunucusuna bağlanmak için [mysql.exe](https://dev.mysql.com/doc/refman/8.0/en/mysql.html) ya da [MySQL çalışma ekranı](./connect-workbench.md)--> seçebilirsiniz. 

Aşağıdaki örnek, MySQL komut satırı arabirimi kullanılarak sunucunuza nasıl bağlanılacağını gösterir. `--ssl-mode=REQUIRED`TLS/SSL sertifika doğrulamasını zorlamak için bağlantı dizesi ayarını kullanın. Yerel sertifika dosya yolunu `--ssl-ca` parametreye geçirin. Değerleri gerçek sunucu adı ve parolasıyla değiştirin. 

```bash
 mysql.exe -h mydemoserver.mysql.database.azure.com -u myadmin -p --ssl-mode=REQUIRED --ssl-ca=c:\ssl\DigiCertGlobalRootCA.crt.pem
```
> [!Note]
> Geçirilen değerin, `--ssl-ca` kaydettiğiniz sertifikanın dosya yolu ile eşleştiğinden emin olun.

### <a name="verify-the-tlsssl-connection"></a>TLS/SSL bağlantısını doğrulama

TLS/SSL kullanarak MySQL sunucunuza bağlandığınızdan emin olmak için MySQL **Status** komutunu yürütün:

```dos
mysql> status
```
Çıktıyı inceleyerek bağlantının şifrelendiğini onaylayın; bu, şunu göstermelidir:  **SSL: kullanımdaki ŞIFRE AES256-SHA**. Bu şifre paketi bir örnek gösterir ve istemciye dayalı olarak farklı bir şifre paketi görebilirsiniz.

## <a name="ensure-your-application-or-framework-supports-tls-connections"></a>Uygulamanızın veya çerçevesinin TLS bağlantılarını desteklediğinden emin olun

Veritabanı Hizmetleri için MySQL kullanan bazı uygulama çerçeveleri, yükleme sırasında varsayılan olarak TLS 'yi etkinleştirmez. MySQL sunucunuz TLS bağlantılarını zorluyor, ancak uygulama TLS için yapılandırılmamışsa, uygulama veritabanı sunucunuza bağlanamadan başarısız olabilir. TLS bağlantılarının nasıl etkinleştirileceğini öğrenmek için uygulamanızın belgelerine başvurun.

## <a name="sample-code"></a>Örnek kod
Azure portal sunucunuz için kullanılabilir olan "bağlantı dizeleri" sayfasında önceden tanımlanmış bağlantı dizeleri, TLS/SSL kullanarak veritabanı sunucunuza bağlanmak için gereken ortak diller için gerekli parametreleri içerir. TLS/SSL parametresi bağlayıcıya göre değişir. Örneğin, "useSSL = true", "sslmode = Required" veya "ssl_verify_cert = true" ve diğer Çeşitlemeler.

Uygulamanızdaki TLS/SSL üzerinden esnek sunucunuza şifreli bir bağlantı kurmak için aşağıdaki kod örneklerine bakın:

### <a name="wordpress"></a>WordPress
[SSL ortak sertifikasını](https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem) indirin ve satırdan sonra wp-config. php içine aşağıdaki satırları ekleyin ```// ** MySQL settings - You can get this info from your web host ** //``` .

```php
//** Connect with SSL** //
define('MYSQL_CLIENT_FLAGS', MYSQLI_CLIENT_SSL);
//** SSL CERT **//
define('MYSQL_SSL_CERT','/FULLPATH/on-client/to/DigiCertGlobalRootCA.crt.pem');
```

### <a name="php"></a>PHP

```php
$conn = mysqli_init();
mysqli_ssl_set($conn,NULL,NULL, "/var/www/html/DigiCertGlobalRootCA.crt.pem", NULL, NULL);
mysqli_real_connect($conn, 'mydemoserver.mysql.database.azure.com', 'myadmin', 'yourpassword', 'quickstartdb', 3306, MYSQLI_CLIENT_SSL);
if (mysqli_connect_errno($conn)) {
die('Failed to connect to MySQL: '.mysqli_connect_error());
}
```

### <a name="php-using-pdo"></a>PHP (PDO kullanarak)

```phppdo
$options = array(
    PDO::MYSQL_ATTR_SSL_CA => '/var/www/html/DigiCertGlobalRootCA.crt.pem'
);
$db = new PDO('mysql:host=mydemoserver.mysql.database.azure.com;port=3306;dbname=databasename', 'myadmin', 'yourpassword', $options);
```

### <a name="python-mysqlconnector-python"></a>Python (MySQLConnector Python)

```python
try:
    conn = mysql.connector.connect(user='myadmin',
                                   password='yourpassword',
                                   database='quickstartdb',
                                   host='mydemoserver.mysql.database.azure.com',
                                   ssl_ca='/var/www/html/DigiCertGlobalRootCA.crt.pem')
except mysql.connector.Error as err:
    print(err)
```

### <a name="python-pymysql"></a>Python (PyMySQL)

```python
conn = pymysql.connect(user='myadmin',
                       password='yourpassword',
                       database='quickstartdb',
                       host='mydemoserver.mysql.database.azure.com',
                       ssl={'ca': '/var/www/html/DigiCertGlobalRootCA.crt.pem'})
```

### <a name="django-pymysql"></a>Docgo (PyMySQL)

```python
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.mysql',
        'NAME': 'quickstartdb',
        'USER': 'myadmin',
        'PASSWORD': 'yourpassword',
        'HOST': 'mydemoserver.mysql.database.azure.com',
        'PORT': '3306',
        'OPTIONS': {
            'ssl': {'ca': '/var/www/html/DigiCertGlobalRootCA.crt.pem'}
        }
    }
}
```

### <a name="ruby"></a>Ruby

```ruby
client = Mysql2::Client.new(
        :host     => 'mydemoserver.mysql.database.azure.com',
        :username => 'myadmin',
        :password => 'yourpassword',
        :database => 'quickstartdb',
        :sslca => '/var/www/html/DigiCertGlobalRootCA.crt.pem'
    )
```

### <a name="golang"></a>Golang

```go
rootCertPool := x509.NewCertPool()
pem, _ := ioutil.ReadFile("/var/www/html/DigiCertGlobalRootCA.crt.pem")
if ok := rootCertPool.AppendCertsFromPEM(pem); !ok {
    log.Fatal("Failed to append PEM.")
}
mysql.RegisterTLSConfig("custom", &tls.Config{RootCAs: rootCertPool})
var connectionString string
connectionString = fmt.Sprintf("%s:%s@tcp(%s:3306)/%s?allowNativePasswords=true&tls=custom",'myadmin' , 'yourpassword', 'mydemoserver.mysql.database.azure.com', 'quickstartdb')
db, _ := sql.Open("mysql", connectionString)
```

### <a name="java-mysql-connector-for-java"></a>Java (Java için MySQL Bağlayıcısı)

```java
# generate truststore and keystore in code
String importCert = " -import "+
    " -alias mysqlServerCACert "+
    " -file " + ssl_ca +
    " -keystore truststore "+
    " -trustcacerts " +
    " -storepass password -noprompt ";
String genKey = " -genkey -keyalg rsa " +
    " -alias mysqlClientCertificate -keystore keystore " +
    " -storepass password123 -keypass password " +
    " -dname CN=MS ";
sun.security.tools.keytool.Main.main(importCert.trim().split("\\s+"));
sun.security.tools.keytool.Main.main(genKey.trim().split("\\s+"));

# use the generated keystore and truststore
System.setProperty("javax.net.ssl.keyStore","path_to_keystore_file");
System.setProperty("javax.net.ssl.keyStorePassword","password");
System.setProperty("javax.net.ssl.trustStore","path_to_truststore_file");
System.setProperty("javax.net.ssl.trustStorePassword","password");

url = String.format("jdbc:mysql://%s/%s?serverTimezone=UTC&useSSL=true", 'mydemoserver.mysql.database.azure.com', 'quickstartdb');
properties.setProperty("user", 'myadmin');
properties.setProperty("password", 'yourpassword');
conn = DriverManager.getConnection(url, properties);
```

### <a name="java-mariadb-connector-for-java"></a>Java (Java için MariaDB Bağlayıcısı)

```java
# generate truststore and keystore in code
String importCert = " -import "+
    " -alias mysqlServerCACert "+
    " -file " + ssl_ca +
    " -keystore truststore "+
    " -trustcacerts " +
    " -storepass password -noprompt ";
String genKey = " -genkey -keyalg rsa " +
    " -alias mysqlClientCertificate -keystore keystore " +
    " -storepass password123 -keypass password " +
    " -dname CN=MS ";
sun.security.tools.keytool.Main.main(importCert.trim().split("\\s+"));
sun.security.tools.keytool.Main.main(genKey.trim().split("\\s+"));

# use the generated keystore and truststore
System.setProperty("javax.net.ssl.keyStore","path_to_keystore_file");
System.setProperty("javax.net.ssl.keyStorePassword","password");
System.setProperty("javax.net.ssl.trustStore","path_to_truststore_file");
System.setProperty("javax.net.ssl.trustStorePassword","password");

url = String.format("jdbc:mariadb://%s/%s?useSSL=true&trustServerCertificate=true", 'mydemoserver.mysql.database.azure.com', 'quickstartdb');
properties.setProperty("user", 'myadmin');
properties.setProperty("password", 'yourpassword');
conn = DriverManager.getConnection(url, properties);
```

### <a name="net-mysqlconnector"></a>.NET (MySqlConnector)

```csharp
var builder = new MySqlConnectionStringBuilder
{
    Server = "mydemoserver.mysql.database.azure.com",
    UserID = "myadmin",
    Password = "yourpassword",
    Database = "quickstartdb",
    SslMode = MySqlSslMode.VerifyCA,
    SslCa = "DigiCertGlobalRootCA.crt.pem",
};
using (var connection = new MySqlConnection(builder.ConnectionString))
{
    connection.Open();
}
```

## <a name="next-steps"></a>Sonraki adımlar
- [MySQL için Azure veritabanı esnek sunucusu 'na bağlanmak ve veri sorgulamak için MySQL çalışma ekranı kullanma](./connect-workbench.md)
- [MySQL kullanarak MySQL için Azure veritabanı esnek sunucusu 'na bağlanma ve veri sorgulama](./connect-php.md)
- [Azure CLI kullanarak MySQL Için Azure veritabanı esnek sunucu sanal ağını oluşturun ve yönetin](./how-to-manage-virtual-network-cli.md).
- [MySQL Için Azure veritabanı esnek sunucusu 'nda ağ](./concepts-networking.md) hakkında daha fazla bilgi edinin
- [MySQL Için Azure veritabanı esnek sunucu güvenlik duvarı kuralları](./concepts-networking.md#public-access-allowed-ip-addresses) hakkında daha fazla bilgi
