# SNMP MIB Scalar Object
Created: 2022-07-04 20:03

🎯 **Goal**: the syntax of MIB (SMIv2)

## Concepts
🧠 **OBJECT IDENTIFIER (OID)**: all SNMP definitions, MIBs, objects, etc..., are identified by their OID in the **global OID tree**

## The Global OID Tree
An enterprise can obtain a sub-node in the `enterprises` sub-branch of this OID tree. 🎦 An example with Sun Microsystems owns the subnode **42**.
`sun OBJECT IDENTIFIER ::= { iso(1) org(3) dod(6) internet(1) private(4) enterprises(1) 42 }`
Therefore, a whole sub-tree reserved for JVM-MANAGEMENT-MIB has root defined at 
`sun 2 jmgt(145) standard(3) jsr163(163) 1`
So with the OID of `jvmMgtMIB` at the root of JVM-MANGEMENT-MIB in dot notation is: _1.3.6.1.4.1.42.2.145.3.163.1_

## SNMP and OBJECT IDENTIFIERS
OIDs are not only used to register global definitions, but also to identify the corresponding pieces of data that can be retrieved from an SNMP Agent implementing these definitions.

🎦 A scalar variable represents the current count of living threads. It is defined by the following OBJECT-TYPE definition in JVM-MANAGEMENT-MIB:
```
jvmMgtMIBObjects OBJECT IDENTIFIER ::= { jvmMgtMIB 1 }
[..**.**]
jvmThreading     OBJECT IDENTIFIER ::= { jvmMgtMIBObjects 3 }
```

```
-- The following objects are mapped from the ThreadMXBean interface.
jvmThreadCount OBJECT-TYPE
    SYNTAX      Gauge32
    MAX-ACCESS  read-only
    STATUS      current
    DESCRIPTION
	"The current number of live threads"
	::= { jvmThreading 1 }
```
👉 To get the `jvmThreadCount` from an SNMP agent that implements the JVM-MANAGEMENT-MIB, send an SNMP GET request containing the OID:

```
1.3.6.1.4.1.42.2.145.3.163.1.1.3.1.0
\^---sun-----\^              
\^--------jvmMgtMIB---------\^    
\^-----------jvmThreadCount-------\^ 
\^------value of jvmThreadCount-----\^
```

❗ Only terminal values (leaves of the tree) can be accessed through the SNMP protocol. 👉Scalar variables are accessed with a trailing **.0** in their OID.
🌟 **Another way**: MIB contains the definitions (`_jvmThreadCount_`), while the agent contains only the actual associated values (`_jvmThreadCount**.**0_`).


## References
1. [[SNMP MIB Columnar Object]]

---
tags: #snmp	#mib