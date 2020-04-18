# How to revoke a cert.


```
acme.sh  --revoke  -d example.com
```

You can also specify a reason for revocation:

```
acme.sh --revoke  -d example.com   --revoke-reason 0
```

The valid reasons are `0-10`,
See:
https://tools.ietf.org/html/rfc5280#section-5.3.1

```
 unspecified             (0),
        keyCompromise           (1),
        cACompromise            (2),
        affiliationChanged      (3),
        superseded              (4),
        cessationOfOperation    (5),
        certificateHold         (6),
             -- value 7 is not used
        removeFromCRL           (8),
        privilegeWithdrawn      (9),
        aACompromise           (10
```
