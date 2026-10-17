# Intune Policy Definitions

**Project:** Device Provisioning, Compliance & Retirement  
**Organization:** Ironbridge Bank & Trust (simulated)  
**Test device:** IB-LAPTOP-01  
**Assigned group:** All devices

## Purpose

These policies configure a managed Windows 11 device and check whether it meets the bank's security requirements. A policy assignment alone does not prove that a setting took effect, so each policy includes a way to verify the result.

## 1. BitLocker encryption configuration

**Intune policy:** `IB-Config-BitLocker-Encryption`  
**Location:** Endpoint security > Disk encryption  
**Platform/profile:** Windows > BitLocker

| Setting | Configured value | Reason |
| --- | --- | --- |
| Require Device Encryption | Enabled | Protects data on a lost or stolen drive from being read outside the device. |
| Warning for other disk encryption | Blocked or disabled, according to the option available in the tenant | Allows encryption to start without waiting for the user to respond to a warning. |
| Assignment | All devices | Applies the requirement to enrolled devices in this lab. |

**Verification:** On IB-LAPTOP-01, `manage-bde -status` showed the operating system drive as **Fully Encrypted** with **Protection On**. The device's Recovery keys page in Intune showed a stored BitLocker recovery key.

**Why the recovery key matters:** Encryption can make data inaccessible to the bank as well as to a thief. Escrowing the recovery key allows authorized recovery when the normal unlock method fails.

## 2. Screen lock configuration

**Intune policy:** `IB-Config-ScreenLock`  
**Location:** Devices > Configuration  
**Platform/profile:** Windows 10 and later > Settings catalog

| Setting | Configured value | Reason |
| --- | --- | --- |
| Device Password Enabled | Enabled | Requires an unlock credential. |
| Max Inactivity Time Device Lock | 5 minutes | Limits access when a user leaves the device unattended. |
| Assignment | All devices | Applies the setting to enrolled devices in this lab. |

**Verification:** Check the policy's device status in Intune and confirm that the test device locks after the configured idle period.

## 3. Compliance baseline

**Intune policy:** `IB-Compliance-Baseline`  
**Location:** Devices > Compliance  
**Platform/profile:** Windows 10 and later > Windows 10/11 compliance policy

| Requirement | Configured value | Reason |
| --- | --- | --- |
| Require BitLocker | Require | Identifies a device that does not meet the encryption requirement. |
| Require Secure Boot | Require | Checks that the device starts with Secure Boot enabled. |
| Minimum OS version | The version entered during setup: `REPLACE_WITH_YOUR_ACTUAL_VERSION` | Flags a device below the chosen Windows version floor. |
| Require a password to unlock mobile devices | Require | Checks for an unlock credential. |
| Action for noncompliance | Mark device noncompliant immediately | Makes a failed requirement visible without a grace period. |
| Assignment | All devices | Evaluates enrolled devices in this lab. |

**Verification:** In Intune, open IB-LAPTOP-01 > Device compliance > `IB-Compliance-Baseline` to see the overall result and the result for each setting.

> **Before publishing:** Replace the minimum OS version above with the value actually saved in your policy. The Phase 3 instructions specify four compliance requirements. Do not claim a fifth requirement, such as antivirus, unless you added and verified it in Intune.

## 4. Compliance test and recovery

To test the baseline, I temporarily changed its minimum OS version to `10.0.30000.0`, a version above the test device's installed version. After the device synced and Intune evaluated it, the device reported **Not compliant**, with **Minimum OS version** identified as the failed requirement.

I then restored the minimum OS version to the value appropriate for the installed build, synced the device again, and confirmed that it returned to **Compliant**.

This test shows the difference between configuring a device and checking its state: the configuration policies set requirements, while the compliance policy reports whether the device meets them.

## 5. Evidence

- `P2-07-both-policies.png` -- encryption and screen lock policies
- `P2-08-compliance-policy.png` -- compliance baseline
- `P2-09-manage-bde-encrypted.png` -- encryption verified on the device
- `P2-10-compliant-and-recovery-key.png` -- compliance and recovery key evidence
- `P2-11-noncompliant-with-reason.png` -- failed minimum OS version check
- `P2-12-compliant-again.png` -- recovered compliance state

**Lab limitation:** This is a simulated bank environment with one test VM. The policy assignments and test results demonstrate the control on that device; they do not establish coverage across a real bank's fleet.

