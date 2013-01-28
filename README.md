# ws; forked.
This project is a fork of the ws WebSocket module built for NodeJS. This for adds support for TLS/SSL communications between a ws server and another WebSocket client. 

Usage
---
Refer to the [official ws documentation](https://github.com/einaros/ws) for detailed usage information. 

Additions
---
To support encryped SSL/TLS connections, instantiate your server as follows:

	var WebSocketServer = require('ws').Server
	  , wss = new WebSocketServer( { 
		    port: port, 
		    key: 'server.key', 
		    cert: 'server.crt',
		    ca: 'ca.crt',
		    password: 'password'
		  }
	  );

If you don't specify a key, or cert WS will assume you want a standard (unsecure) connection. 

Generating Server Certs
---
There is a wealth of knowledge on the googles regarding generating self signed certificates. For the lazy though, I assume you have openssl installed, run the following commands: 

Create your CA root certificate:

	$> openssl genrsa -des3 -out ca.key 1024
	$> openssl req -new -key ca.key -out ca.csr
	$> openssl x509 -req -days 365 -in ca.csr -out ca.crt -signkey ca.key

Create your self signed server certificate: 

	$> openssl genrsa -des3 -out server.key 1024
	$> openssl req -new -key server.key -out server.csr
	$> openssl x509 -req -in server.csr -out server.crt -CA ca.crt -CAkey ca.key -CAcreateserial -days 365

	
Veryifying Your Server
---
OpenSSL comes with some cool tools. One of those tools is the s_client utility. Use this to verify that your server is accepting connections and using its server certificate. From the command line run the following command:

	$> openssl s_client -connect localhost:3001 
	
This should produce output like this:

	CONNECTED(00000003)
	depth=0 /C=AU/ST=Some-State/O=Internet Widgits Pty Ltd
	verify error:num=18:self signed certificate
	verify return:1
	depth=0 /C=AU/ST=Some-State/O=Internet Widgits Pty Ltd
	verify return:1
	---
	Certificate chain
	 0 s:/C=AU/ST=Some-State/O=Internet Widgits Pty Ltd
	   i:/C=AU/ST=Some-State/O=Internet Widgits Pty Ltd
	---
	Server certificate
	-----BEGIN CERTIFICATE-----
	MIICATCCAWoCCQCApxN9wxSEbzANBgkqhkiG9w0BAQUFADBFMQswCQYDVQQGEwJB
	VTETMBEGA1UECBMKU29tZS1TdGF0ZTEhMB8GA1UEChMYSW50ZXJuZXQgV2lkZ2l0
	cyBQdHkgTHRkMB4XDTEzMDEyODE0NDAzOFoXDTE0MDEyODE0NDAzOFowRTELMAkG
	A1UEBhMCQVUxEzARBgNVBAgTClNvbWUtU3RhdGUxITAfBgNVBAoTGEludGVybmV0
	IFdpZGdpdHMgUHR5IEx0ZDCBnzANBgkqhkiG9w0BAQEFAAOBjQAwgYkCgYEA60fO
	lDviuIgocgN0LcuphxDM1b6TaYdExAqZ0usBC5AAkxNvBPjjdXIFeYq4xrvYnaB3
	3u38NyLgxBsoPACxb7Xu3sYmTHiKzDF20cuk2ISQEvm7EHUfAeuoi0kfbTuXww0o
	rcfcVI0P4YZOH2YLaUgZitOfJgZ8YaHN0+b3RnECAwEAATANBgkqhkiG9w0BAQUF
	AAOBgQB+mNV3FVb8BsYWe2ibgb2BtU40JoGFupcXM9TKgtN1r+2++6aw7PEEkm4n
	/wduFssVhUCz0YhTK5zqyPMbmGRr7bL2NPLdT11e89eN319iAnTxW5KuGlqlD6yO
	9JtKhI2e3MaGYAzTCj1roltRM7mWai19/datDziS5pqEMTPN2w==
	-----END CERTIFICATE-----
	subject=/C=AU/ST=Some-State/O=Internet Widgits Pty Ltd
	issuer=/C=AU/ST=Some-State/O=Internet Widgits Pty Ltd
	---
	No client certificate CA names sent
	---
	SSL handshake has read 686 bytes and written 328 bytes
	---
	New, TLSv1/SSLv3, Cipher is AES256-SHA
	Server public key is 1024 bit
	Secure Renegotiation IS supported
	Compression: NONE
	Expansion: NONE
	SSL-Session:
	    Protocol  : TLSv1
	    Cipher    : AES256-SHA
	    Session-ID: D94CCF80BBF632E4F3E5E2FF7C71C434A29BFB8F670989AFAC9DEA4DBE1DCF96
	    Session-ID-ctx: 
	    Master-Key: F7E9BF8F559675A305989B64C69F889944537B4CB9BA05869F1E5A5D8D10807CBBD7338E93327E54538C3C958F886C53
	    Key-Arg   : None
	    Start Time: 1359402510
	    Timeout   : 300 (sec)
	    Verify return code: 18 (self signed certificate)
	---
	
Success. The last line: `Verify return code: 18 (self signed certificate)` indicates you are using a certificate authority you've generated yourself. 
