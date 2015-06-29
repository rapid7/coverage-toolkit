# Coverage: Toolkit

The coverage toolkit provides customers with a mechanism for contributing content that includes protocol fingerprinting, unauthenticated (remote) vulnerability coverage, and other future collaboration projects.  Customers should find the coverage toolkit intuitive and simple.  Feedback is welcomed and encouraged.

# Scanning

To scan with one or more XML protocol fingerprints, place the XML files into the "plugins/proto-fp/custom" directory of the scan engine you want to scan with.  If the scan engine is part of the console (i.e. the local scan engine), you will need to put the XML files into "plugins/proto-fp/custom" on the console.  The scan engine does not need to be restarted.  When you start a scan, the scan engine will load XML files in the "plugins/proto-fp/custom" directory.  You can make changes to the XML files in the "plugins/proto-fp/custom" directory at any time and future scans will use the latest version of the XML files at the point in time when the scan is started.

The "plugins/proto-fp/builtin" directory is used for referencing XML protocol fingerprints described in this repo.  These XML protocol fingerprints are contributed by you, and are maintained by Rapid7.  These XML protocol fingerprints come from the coverage-toolkit repo.  We will verify with every release that these XML protocol fingerprints continue to function appropriately.  Note that changes to files in this directory are not guaranteed to remain after a content and/or product update.

The "plugins/proto-fp/custom" directory is used by you for XML protocol fingerprints associated with protocols that might be proprietary to your environment.  If you do not want to share these XML protocol fingerprints, you can place them in the "plugins/proto-fp/custom" directory, and they will never be modified or deleted as part of a content and/or product update.

# Fingerprints

The protocol fingerprint is described using a simple XML format.  A protocol can contain multiple fingerprints and each fingerprint is tried until at least one is successful.  If none of the fingerprints are successful the protocol is skipped.  There is no limitation on duplicate protocols in the framework, and multiple protocols of the same name can be added with different checks for different ports if such a technique is required for performance or accuracy.

Example:
```
<Protocol frameworkVersion='1.0' name='ident' tls='false'>
 <Applicable layer4='TCP' port='113' preference='1.0'/>
 <Fingerprint>
  <Send request="0, 0\r\n"/>
  <Match response="^[0-9]+[ ]*,[ ]*[0-9]+[ ]*:[ ]*(?:ERROR|USERID)[ ]*:.*"/>
 </Fingerprint>
</Protocol>
```

## Protocol Framework Version (frameworkVersion)

The protocol framework version, currently 1.0, influences what the XML format of the protocol fingerprint will look like.  As we develop this feature, the protocol framework version will be incremented, and it is possible that future versions of this future will look very different from what the current protocol framework version looks like.  The Nexpose product will maintain backward compatibility with prior protocol framework versions for a reasonable amount of time.  The Nexpose team will update legacy protocol fingerprints to newer versions when applicable.  It is recommended that customers contribute their protocol fingerprints to the coverage toolkit project so we can maintain them, and update them, when their protocol framework version is deprecated.

## Protocol Name (name)

The protocol name is the name displayed in the UI, in reports, and used by vulnerability content in a scan.  The name should be associated with the official name of the protocol and in some rare cases, the name might include the letter "s" when the protocol is encapsulated in SSL/TLS i.e. HTTP versus HTTPS.

## TLS (tls)

The TLS attribute declares if the protocol being fingerprinted is encapsulated in the SSL/TLS protocol.  If true, a SSL/TLS handshake will be done before evaluation of the XML protocol fingerprint.  If false, a SSL/TLS handshake will not be done before evaluation of the XML protocol fingerprint.

## Applicability (Applicable)

A protocol fingerprint is tested against a port, protocol, and IP type, based on an applicability test.  The applicability test includes the probability that a protocol fingerprint is associated with a particular port, layer 4 protocol (TCP or UDP), and layer 3 protocol (IPv4 or IPv6).  The port can be described as a regular expression i.e. every port from 80-89 can be described as "8[0-9]".  The preference value must be greater than 0.0, less than or equal to 1.0, and is associated with the probability the protocol can be found on the layer 3, layer 4, and port(s) associated with the applicable test.  Multiple applicable tests can exist in a single XML protocol fingerprint.

Example:
```
<!-- The protocol fingerprint is applicable to IPv4, but not IPv6. -->
<!-- The protocol fingerprint is applicable to TCP, but not UDP. -->
<!-- The protocol fingerprint is applicable to ports 80, 81, 82, 83, 84, 85, 86, 87, 88, and 89. -->
<!-- One of the first XML protocol fingerprinters to run, because preference is 1.0, which is a high preference value. -->
<Applicable layer3='IPv4' layer4='TCP' port='8[0-9]' preference='1.0'/>
<!-- The protocol fingerprint is applicable to IPv4, but not IPv6. -->
<!-- The protocol fingerprint is applicable to TCP, but not UDP. -->
<!-- The protocol fingerprint is applicable to ports 90. -->
<!-- One of the last XML protocol fingerprinters to run, because preference is 0.1, which is a low preference value. -->
<Applicable layer3='IPv4' layer4='TCP' port='90' preference='0.1'/>
```

### Layer 3 (layer3)

The layer 3 protocol associated with the protocol fingerprint.  This can be "IPv4" or "IPv6".  If undefined, the protocol fingerprint will run against both "IPv4" and "IPv6".

Example:
```
<!-- Run against IPv4 addresses only. -->
<Applicable layer3='IPv4'/>
<!-- Run against IPv6 addresses only. -->
<Applicable layer3='IPv6'/>
<!-- Run against IPv4 and IPv6 addresses. -->
<Applicable/>
```

### Layer 4 (layer4)

The layer 4 protocol associated with the protocol fingerprint.  This can be "TCP" or "UDP".  If undefined, the protocol fingerprint will run against both "TCP" and "UDP".

Example:
```
<!-- Run against TCP addresses only. -->
<Applicable layer4='TCP'/>
<!-- Run against UDP addresses only. -->
<Applicable layer4='UDP'/>
<!-- Run against TCP and UDP addresses. -->
<Applicable/>
```

### Port (port)

The port(s) associated with the protocol fingerprint.  This is a regular expression that must match the numeric value of a port for the protocol fingerprinter to be applicable to that port and run against it.  The regular expression must match the entire numeric value of a port, for example, a single "." regular expression will match a single digit and will only make single digit ports applicable while multiple digit ports will not be applicable.  Supported regular expression operations can be found here: http://docs.oracle.com/javase/7/docs/api/java/util/regex/Pattern.html.  If undefined, the protocol fingerprint will run against all ports.

Example:
```
<!-- Run against port 80 only. -->
<Applicable port='80'/>
<!-- Run against port 80, 81, 82, 83, 84, 85, 86, 87, 88, and 89 only. -->
<Applicable port='8[0-9]'/>
<!-- Run against port 80, 8000, and 8080. -->
<Applicable port='80|8000|8080'/>
<!-- Run against all ports. -->
<Applicable port='.*'/>
<!-- Run against all ports. -->
<Applicable/>
```

### Preference (preference)

The preference is the probability the protocol fingerprint will be successful against a given layer 3, layer 4, and port association.  For example, it is common that HTTP exists on any layer 3 protocol, the TCP layer 4 protocol, and port 80.  However, if you want to look for HTTP on every port, you will include another applicability test that includes all ports, but with a lower preference value.  It is important to use a lower preference value because it allows other protocol fingerprints that are more likely to exist those other ports being tested, to run first.  When a protocol runs on an uncommon port it is called a non-standard port for that protocol.  The preference value itself is a float can be any value greater than 0.0, less than or equal to 1.0; within reason.

Common preference values:
Highly likely is 1.0
Usually likely is between 0.8 and 1.0
Less likely is between 0.5 and 0.8
Least likely is between 0.1 and 0.5
Almost never likely is between 0.0 and 0.1

Example:
```
<!-- Run the fingerprint against TCP port 80 before most other fingerprints run. -->
<Applicable layer4='TCP' port='80' preference='1.0'/>
<!-- Run the fingerprint against all other TCP ports after most other fingerprints have run. -->
<Applicable layer4='TCP' preference='0.1'/>
```

## Fingerprint

The fingerprint portion of the protocol describes how the protocol is identified on the network.  The fingerprint portion can contain a send statement and a match statement.  The send statement will send data that can be described in literals, escaped constants such as \r for a carriage return and \n for a new line character, or an escaped hexidecimal value described as \x00.  The match statement will interpret the response as a regular expression and if the regular expression matches, the fingerprint will progress to the next operation (the next send statement or match statement).  When no next operation exists, the fingerprint is considered successful and the protocol is returned as the protocol for that endpoint (IP address:port/protocol tuple).

Example:
```
<Send request="HEAD / HTTP/1.0\r\n\r\n"/>
<Match response="^HTTP/(?:0\.9|1\.[01])(?:\x20|\r|\n)"/>
```

### Send (request)

The send statement in a fingerprint will send the payload associated with the request attribute to the port being fingerprinted.  The send statement can include hexadecimal encoded values using the "\x" prefix.  There are other common "\" shortcuts listed in the table below.  The send statement can also include literals which are none hexadecimal encoded values and non-slash encoded shortcuts.

Current Supported Slash Encodings
\x## for hexidecimal encoded values.
\\ for sending a single slash character.
\r for sending a carriage return (\x0A in hexidecimal).
\n for sending a new line character (\x0D in hexidecimal).
\t for sending a tab character (\x09 in hexidecimal).
\0 for sending a null character (\x00 in hexidecimal).

Unsupported character slashings will be interpreted as a literal sequence of slash and the following character (i.e. "\3" will send the literal "\3" characters).  However, it is a good idea to escape the slash, for example, "\\3", which will also send "\3".  The reason it is a good idea to escape the slash is if in a future product version the "3" character becomes a shortcut for something.

Example:
```
<!-- Send a DNS request for 1.0.0.127.in-addr.arpa -->
<Send request="\x00\x01\x01\x00\x00\x01\x00\x00\x00\x00\x00\x00\x01\x31\x01\x30\x01\x30\x03\x31\x32\x37\x07\x69\x6e\x2d\x61\x64\x64\x72\x04\x61\x72\x70\x61\x00\x00\x0c\x00\x01"/>
<Match response="\x00\x01.*\x01\x31\x01\x30\x01\x30\x03\x31\x32\x37\x07\x69\x6e\x2d\x61\x64\x64\x72\x04\x61\x72\x70\x61\x00"/>
```

### Match (response)

The match statement in a fingerprint will match the response from the port being fingerprinted against the specified regular expression.  It is not a requirement to send something before matching a response and some protocols, known as verbose protocols, will automatically respond with a message when a successful connection is made.  The match statement can include hexadecimal encoded values using the "\x" prefix.  There are other common "\" shortcuts listed in the table below.  The match statement can also include literals which are none hexadcimal encoded values and non-slash encoded shortcuts.

Current Supported Slash Encodings
\x## for hexidecimal encoded values.
\\ for sending a single slash character.
\r for sending a carriage return (\x0A in hexidecimal).
\n for sending a new line character (\x0D in hexidecimal).
\t for sending a tab character (\x09 in hexidecimal).
\0 for sending a null character (\x00 in hexidecimal).

Unsupported character slashings will be interpreted as a regular expression operation.  See http://docs.oracle.com/javase/7/docs/api/java/util/regex/Pattern.html for an explanation on what regular expression operations are supported.

Example:
```
<!-- Match SMTP and/or FTP banner -->
<Match response="^220(?:\x20|\r|\n)"/>
<!-- Test for SMTP -->
<Send request="EHLO test\r\n"/>
<Match response="^250(?:\x20|\r|\n)"/>
```

# Testing

A work in progress.  It will be possible to run protocol fingerprints using the same framework the Nexpose product uses.  This will give contributors a means for testing their contributions as if they were running Nexpose, but without the need for running Nexpose.  Contributors can also place their protocol fingerprint change(s) into the Nexpose custom protocol fingerprint directory and test them without needing to restart Nexpose.  The next Nexpose scan will automatically see and include any changed or added protocol fingerprint(s).  The Nexpose custom protocol fingerprint directory can be found in the Nexpose installation directory under "plugins/proto-fp".  There are two subdirectories under "plugins/proto-fp" that include "builtin" and "custom".
