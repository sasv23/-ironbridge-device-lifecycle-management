# Device Disposal Procedure

**Organization:** Ironbridge Bank & Trust (simulated environment)

**Owner:** IT Operations and Information Security

**Scope:** Bank-owned endpoints, removable media, and any storage that may
hold customer nonpublic information (NPI).

**Completion criteria:** A device is recorded as *disposed* only after
sanitization, chain of custody, and the asset record are all documented.
Until then its status remains *in process*, *missing*, or *under
investigation*.

## 1. Trigger

A retirement is initiated by hardware failure beyond economical repair, end
of the refresh cycle, employee separation without reassignment, or reported
loss or theft. The retirement ticket records the reason, asset tag, serial
number, assigned user, last known location, and encryption status. Any legal
hold or records-retention requirement is checked before sanitization begins.

Loss or theft is handled as a security incident rather than a disposal. The
user's sessions and access are revoked, a remote wipe is issued through
Intune, and the incident record is preserved. The asset is not marked
*disposed*, because custody of the device cannot be demonstrated. This is the
gap that left Ironbridge unable to say whether the stolen laptop was
encrypted.

## 2. Unenroll

The device is verified by serial number and then removed from Intune so it no
longer counts as a managed endpoint. The administrator, action, and timestamp
are logged.

Retire and Wipe are different actions. Retire removes only company-managed
data, certificates, and apps, so it does not count as media sanitization.
Wipe resets the device to a factory state. Deleting the device record in
Intune is not evidence that the data on the drive is gone. A device that
stops checking in is classified as **missing**, not retired, and is
investigated instead of being dropped from the inventory.

For corporate-owned devices such as IB-LAPTOP-01, Wipe is the appropriate
action. Retire is better suited to BYOD devices, where personal data must be
left untouched.

## 3. Recover

The user's Microsoft 365 license and any other assigned software are
reclaimed, and accessories and removable media are collected. The assigned
asset record for the user is updated. In this lab, the user held a Microsoft
365 E5 license. A license left assigned after decommissioning is a recurring
cost with no business value.

## 4. Destroy the data

Sanitization follows NIST SP 800-88 (Clear, Purge, Destroy). For a bank
holding NPI, the preferred method is **cryptographic erase**, which is a Purge
technique. The operating system drive is BitLocker-encrypted, so destroying
the key protectors, including the recovery key escrowed in Entra ID, makes
the data unrecoverable. An Intune wipe follows to reset the device.

Where the drive is inoperable or was never encrypted, physical destruction is
used. A standard reset alone provides weak evidence that data is gone and is
not sufficient for NPI.

In this lab, BitLocker reported Used Space Only Encrypted at 100% with
Protection On. That covers all data written on a new installation. For a
device that held customer data over a long service life, full-disk encryption
is preferable, because used-space-only encryption can leave previously
deleted data readable in free space.

### Why deleting files is not data destruction

Deleting a file removes the file system pointer, not the underlying data. The
contents remain on the media until they are overwritten, and free recovery
tools can restore them. Emptying the recycle bin or running a quick format
does not meet any sanitization standard. Only cryptographic erase, a secure
wipe to a defined standard, or physical destruction counts as destroying
data.

## 5. Get the certificate

A certificate of destruction is obtained from the disposal vendor. It lists
each device's serial number, the sanitization method, and the date of
destruction, and it is filed with the retirement record. Without it, the bank
has performed disposal but has no audit evidence to present to an examiner.

## 6. Update the inventory

The asset status is set to *retired*, with the disposal date, sanitization
method, and certificate reference. The inventory is the source of truth for
audits. If it drifts from reality, it returns confident wrong answers, such
as reporting a destroyed device as active or hiding one that is missing. A
device is marked *disposed* only after the completion criteria at the top of
this document are met.
