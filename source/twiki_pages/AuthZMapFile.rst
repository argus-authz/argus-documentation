
Authorization Service: Grid Map File Syntax
===========================================

Description
-----------

Grid Map files are used to express a mapping from subject attributes in
to a local account.

File Syntax
-----------

Each line of a grid map file is either pure whitespace, a comment, or a
mapping.

Comments begin with a number or hash sign (#), may be followed with any
character and ends at the end of the line. The following is an example
of grid map file which contains only a single comment.

::

    # This is a map file with a single comment

A mapping line consists of two parts, the first is a key that identifies
the subject that will be mapped. This part is always enclosed in double
quotes ("). The second part is the comma-separated list of targets to
which the subject may be mapped. The first and second parts of the map
line are separated by any number of spaces or tabs. The following is an
example of a grid map file mapping an FQAN to an account.

::

    "/vo/atlas/analysis" .atlas

Subject Keys
------------

The subject key, the first component of a mapping line, may be either an
X.509 DN or a Fully Qualified Attribute Name (FQAN).

If an X.509 DN is used it may be either in the non-standard grid form
(where each component is separated by a forward slash ('/') and the CN
comes last), or the standard RFC2253 format (where components are
separated by a comma (',') and the CN comes first). The RFC2253 format
is preferred. Both formats may be used within a single file and in both
case the following escape sequences may be used:

+-------------------+---------------------------------------------------------------------+
| Escape Sequence   | Represents                                                          |
+===================+=====================================================================+
| ``\'``            | a single quote                                                      |
+-------------------+---------------------------------------------------------------------+
| ``\"``            | a double quote                                                      |
+-------------------+---------------------------------------------------------------------+
| ``\\``            | a backwards slash                                                   |
+-------------------+---------------------------------------------------------------------+
| ``\/``            | a forward slash                                                     |
+-------------------+---------------------------------------------------------------------+
| ``\f``            | a formfeed                                                          |
+-------------------+---------------------------------------------------------------------+
| ``\n``            | a new line                                                          |
+-------------------+---------------------------------------------------------------------+
| ``\r``            | a carriage return                                                   |
+-------------------+---------------------------------------------------------------------+
| ``\t``            | a horizontal tab                                                    |
+-------------------+---------------------------------------------------------------------+
| ``\x##``          | the ASCII character corresponding to the given hexadecimal digits   |
+-------------------+---------------------------------------------------------------------+
| ``\u####``        | the UTF-8 character corresponding to the given hexadecimal digits   |
+-------------------+---------------------------------------------------------------------+

Map Targets
-----------

The map target, the second component of a mapping line, is a comma
separated list of either an account or group names.

A map target prefixed with a dot ``.`` represents a pool account.
