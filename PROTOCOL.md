The protocol can be summarized by the following flow chart:

```ascii
             TLS-over-TLS
             based on pubkey
             in attestation                      
             and SSL cert from                   TLS based on Mozilla's
             registry enclave                    Root CA store
┌─────────┐ ── ── ── ── ── ── ── ┌─────────────┐ ── ── ── ── ── ── ┌─────────────┐ 
│         │ Oracle.py,           │             │ user login creds  │             │
│         │ any dependencies,    │  Oracle     │ ────────────────► │ Some Website│
│ USER    │ e.g. login creds,    │  Enclave    │ ◄──────────────── │             │
│(browser)│ ───────────────────► │ ┌─────────┐ │ data for contract │             │
│         │                      │ │Oracle.py│ │ ── ── ── ── ── ── └─────────────┘
│         │ ◄─────────────────── │ └─────────┘ │ 
│         │ Calldata, Signatures │             │
└─────────┘ ── ── ── ── ── ── ── └─────────────┘ ◄──────────────────┐ If valid:
       │                                │                           │
       │                                │attestation                │ signature over 
       │ $OPN payment                   │CSR (=cert sign request)   │ {Oracle Enclave pubkey, Oracle Provider}
       │                                │                           │
       │ Oracle Enclave pubkey          ▼                           │  SSL cert
       │ Oracle Provider           ┌─────────┐                     ┌──────────┐
       │(Signed by registry)       │Oracle   │ ─────────────────►  │ Registry │
       │                           │Provider │ attestation, CSR    │ Enclave  │
       │ Calldata                  │(on EC2) │ + provider address  └──────────┘
       │ (Signed by Oracle)        └─────────┘
       │                                 ▲
       │                                 │
       │                                 │
       │                  $OPN payments  │                         ┌──────────────────┐
       │           ┌─────────────────────┴──────────────────────►  │ Registry Provider│
       ▼           │                                               └──────────────────┘
   ┌───────────────┴────┐  If valid:         ┌────────────┐
   │OpenContractsHub.sol│ ────────────────►  │Contract.sol│
   └────────────────────┘  submit call       └────────────┘
       │    
       │ $OPN payment   
       ▼ reducing supply   
   ┌─────────────────┐
   │ Burner Address  │
   └─────────────────┘
```

