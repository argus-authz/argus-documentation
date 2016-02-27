%META:TOPICINFO{author="chad\_2elajoie\_40switch\_2ech"
date="1263197554" format="1.1" version="1.2"}%
%META:TOPICPARENT{name="AuthorizationFramework"}%

Argus: PEP Java Client: Command Line
====================================

Command Line Interface
======================

The PEP-J library contains a basic command line utility that is useful
for sending test requests to the PEP daemon.

The command line client, ``bin/testreq.sh``, takes a single parameter,
the path (absolute or relative) path to a client configuration file.
This client will create an empty request and then run any configured
PIPs in order to populate it. The `Static Attribute
PIP <AuthZPEPPIP#Static_Attributes_PIP>`__ is very useful for creating
custom requests with which to test.
