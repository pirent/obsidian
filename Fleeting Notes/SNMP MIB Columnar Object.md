# SNMP MIB Columnar Object
Created: 2022-07-04 20:03

🎯 **Goal**: the syntax of MIB (SMIv2)

🧠 Represent tables which is the most complex thing in SNMP.
ℹ It's complex because:
- SNMP data is registered in a tree structure which does not map quite naturally to 2D tables
- The protocol has no verb for CREATE/INSERT/DELETE (only GET/SET) so all operations in rows has to be performed by means of SET request.

## Defining Tables in SNMP
This defines a table called _jvmRTInputArgsTable_ whose rows are modeled as instances of _JvmRTInputArgsEntry_**.**
```
jvmRTInputArgsTable OBJECT-TYPE
    SYNTAX      SEQUENCE OF **JvmRTInputArgsEntry**
    MAX-ACCESS  not-accessible
    STATUS      current
    DESCRIPTION
	"The Input Argument Table lists the input arguments passed to the Java Virtual Machine.""
	::= { jvmRuntime 20 }
```
## Conceptual Rows
What you can get and set with SNMP, are **individual table cells**, identified by the **OID of the column** you're looking at, and the **index of the _conceptual row_** that intersects with that column. 

So here is how you define a conceptual row:
```
**jvmRTInputArgsEntry** OBJECT-TYPE
    SYNTAX      **JvmRTInputArgsEntry**
    MAX-ACCESS  not-accessible
    STATUS      current
    DESCRIPTION
	"Represent an input argument passed to the Java Virtual Machine."
    INDEX { jvmRTInputArgsIndex }
    ::= { jvmRTInputArgsTable 1 }

**JvmRTInputArgsEntry** ::= SEQUENCE {
    jvmRTInputArgsIndex JvmPositive32TC,
	jvmRTInputArgsItem  JvmArgValueTC
}
```
🥽 **Points of interest**
👓 _jvmRTInputArgsEntry_  is an OBJECT-TYPE that is **not-accessible** because it is not value leave of the OID tree; thus cannot be returned.
👓 the **SYNTAX** clause identifies the pseudo-type of the conceptual row. 🔅 The name of the conceptual row defination and the name of its pseudo-type differ only in the case of their first letter.
👓 The **INDEX** clause identifies the columns whose values will serve to uniquely identify a row in the table (they compose a primary key).
👓 **JvmRTInputArgsEntry** is not an OBJECT-TYPE, but is the definition of the pseudo-type of the conceptual row. It is declared to be a `SEQUENCE` of fields whose name and types correspond to the table's columnar objects

🧠 The only thing will be returned by the SNMP agent is instances of _jvmRTInputArgsItem_ ✅. _jvmRTInputArgsTable_, _jvmRTInputArgsEntry_, or _JvmRTInputArgsEntry_ are not. ❌

## Definition of Index and Non-Index Columnar Objects
Define an index column:
```
jvmRTInputArgsIndex OBJECT-TYPE
    SYNTAX      JvmPositive32TC
    MAX-ACCESS  not-accessible
    STATUS      current
    DESCRIPTION
	"The index of the input argument, as in the array returned by RuntimeMXBean.getInputArguments().""
    ::= { jvmRTInputArgsEntry 1 }
	
JvmPositive32TC ::= TEXTUAL-CONVENTION
    STATUS       current
    DESCRIPTION
          "A positive Integer32**.** In Java that would be a number 
           in [0**.**.Integer.MAX_VALUE]."
	SYNTAX Integer32 (0..2147483647)
```
🥽 **Points of interest**
👓 *jvmRTInputArgsIndex* is declared as **not-accessible** as all indexes are not-accessible.
👓 `JvmPositive32TC` is a TEXTUAL-CONVENTION which is a mean to assign some specific semantics to to a generic base type used in a specific context. 🎦 *Here as value of an index*. When the value of _jvmRTInputArgsIndex_ will be encoded and sent over the wire, it will be encoded as a simple Integer32.

👉 Define the non-index column (the actual data column), 🎦 retrieve each of the `args[i]` input passed to the JVM `public static void main(String[] args)` method.
```
jvmRTInputArgsItem OBJECT-TYPE
    SYNTAX      JvmArgValueTC
    MAX-ACCESS  read-only
    STATUS      current
    DESCRIPTION
	"An input argument at index jvmRTInputArgsIndex, as in the array returned by RuntimeMXBean.getInputArguments()."
  ::= { jvmRTInputArgsEntry 2 }
```

## Getting an instance of a Columnar Object
🎦 To get the value of args[i], send a GET request for the following OID: `jvmRTInputArgsItem.<i>` or in dot notation, and assuming that `i=5`:
```
1.3.6.1.4.1.42.2.145.3.163.1.1.4.20.1.2.5
\^---sun-----\^             
\^------jvmMgtMIB-----------\^     
\^--------jvmRTInputArgsTable-----\^  
\^--------jvmRTInputArgsEntry--------\^ 
\^----------jvmRTInputArgsItem---------\^ 
\^------value of jvmRTInputArgsItem------\^
         at jvmRTInputArgsIndex=5 
```

## References
1. [[SNMP MIB Scalar Object]]

---
tags: #snmp	#mib