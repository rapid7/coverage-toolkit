# Coverage: Toolkit

The coverage toolkit provides customers with a mechanism for contributing content that includes protocol fingerprinting, unauthenticated (remote) vulnerability coverage, and other future collaboration projects.  Customers should find the coverage toolkit intuitive and simple.  Feedback is welcomed and encouraged.

# Fingerprints

The protocol fingerprint is described using a simple XML format.  A protocol can contain multiple fingerprints and each fingerprint is tried until at least one is successful.  If none of the fingerprints are successful the protocol is skipped.  There is no limitation on duplicate protocols in the framework, and multiple protocols of the same name can be added with different checks for different ports if such a technique is required for performance or accuracy.

Example:
```
<Protocol frameworkVersion='1.0' name='ident' tls='false'>
 <Applicable protocol='TCP' port='113' preference='1.0'/>
 <Fingerprint>
  <Send request="0, 0\r\n"/>
  <Match response="^[0-9]+[ ]*,[ ]*[0-9]+[ ]*:[ ]*(?:ERROR|USERID)[ ]*:.*"/>
 </Fingerprint>
</Protocol>
```

## Protocol Framework Version

The protocol framework version, currently 1.0, influences what the XML format of the protocol fingerprint will look like.  As we develop this feature, the protocol framework version will be incremented, and it is possible that future versions of this future will look very different from what the current protocol framework version looks like.  The Nexpose product will maintain backward compatibility with prior protocol framework versions for a reasonable amount of time.  The Nexpose team will update legacy protocol fingerprints to newer versions when applicable.  It is recommended that customers contribute their protocol fingerprints to the coverage toolkit project so we can maintain them, and update them, when their protocol framework version is deprecated.

## Applicability

A protocol is tested against a port, protocol, and IP type, based on an applicability test.  The applicability test includes the probability that a protocol is associated with a particular port, layer 4 protocol (TCP or UDP) and IP type (IPv4 or IPv6).  If undefined, either layer 4 protocol, or IP type, is tested.  The port can be described as a regular expression i.e. every port from 80-89 can be described as "8[0-9]".

## Fingerprint

The fingerprint portion of the protocol describes how the protocol is identified on the network.  The fingerprint portion can contain a send statement and a match statement.  The send statement will send data that can be described in literals, escaped constants such as \r for a carriage return and \n for a new line character, or an escaped hexidecimal value described as \x00.  The match statement will interpret the response as a regular expression and if the regular expression matches, the fingerprint will progress to the next operation (the next send statement or match statement).  When no next operation exists, the fingerprint is considered successful and the protocol is returned as the protocol for that endpoint (IP address:port/protocol tuple).

# Testing

A work in progress.  It will be possible to run protocol fingerprints using the same framework the Nexpose product uses.  This will give contributors a means for testing their contributions as if they were running Nexpose, but without the need for running Nexpose.  Contributors can also place their protocol fingerprint change(s) into the Nexpose custom protocol fingerprint directory and test them without needing to restart Nexpose.  The next Nexpose scan will automatically see and include any changed or added protocol fingerprint(s).  The Nexpose custom protocol fingerprint directory can be found in the Nexpose installation directory under "plugins/proto-fp".  There are two subdirectories under "plugins/proto-fp" that include "builtin" and "custom".
