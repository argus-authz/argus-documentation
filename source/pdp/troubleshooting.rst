.. _argus_pdp_troubleshooting:

Argus: Policy Decision Point (PDP): Troubleshooting
===================================================

PDP Uses "Old" Policies
-----------------------

The PDP caches policies received from the PAP in order to avoid the cost
of fetching and parsing them for every request. In cases where you know,
or suspect, the policy used by the PDP is no longer in synch with the
policy stored at the PAP you may use the ``pdpctl reloadPolicy`` command
to force the PDP to flush its policy cache and retrieve the latest
policy from the PAP.

Private Key File Access
-----------------------

Many systems protect their private keys so that only super-user accounts
can read them. Starting, and running the PDP, as such an account is
strongly discouraged. The recommend approach is to create a special
group (e.g. 'hostkey') that has read permissions to the key and ensure
the user running the PDP service is also in this group. This group
should **not** have write permission to the key.

Some people might view this as a loss of security, because, if the
service user account were compromised the attacker would be able to read
the private key. However, the service holds a copy of key in memory once
it starts and this copy can easily be accessed via tools that come with
the JRE.
