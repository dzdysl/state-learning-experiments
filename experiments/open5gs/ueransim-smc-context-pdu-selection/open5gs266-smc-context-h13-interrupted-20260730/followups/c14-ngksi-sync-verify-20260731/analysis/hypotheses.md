# Source-correlated interpretation

## UERANSIM template register

Let `r_template` be the reusable `registrationRequestGUTI.ngKSI` field and
`r_current` the accepted UE current-context KSI. The replacement behavior fits:

```text
on accepted SMC(k):
    r_current' = k
    r_template' = k

on current-context clear:
    r_current' = absent
    r_template' = r_template

on registrationRequestGUTI send:
    transmitted_ngKSI = r_template
```

This matches 36/36 aligned update-and-use chains in repetitions 2-10. The
critical discriminator is the first GUTI after deregistration: the trace says
no current context exists at send time, but the transmitted template still
contains the immediately preceding accepted SMC KSI. A send-time-only refresh
cannot derive that value from a missing current context.

Confidence is high for the behavioral equation and medium-high for correlation
to commit `6b1bcc070331ac2d0443a255afbf5466413fd0b7`, because the source update
site is exact but the deployed UE binary/archive hash is absent.

## AMF black-box candidate

The replacement does not change the previously inferred AMF edge relations:

```text
AuthenticationRequest.KSI = (declared_registration_KSI + 1) mod 7
SecurityModeCommand.KSI = latest AuthenticationRequest.KSI
```

The Open5GS v2.6.6 source snapshot contains compatible increment and copy
sites, but the register names are not needed to establish UE template sync.
The effective C14 `+2 mod 7` whole-cycle recurrence results from two accepted
authentication/security-context updates per nine-edge route, not from output
instability.
