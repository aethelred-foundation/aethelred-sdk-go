<h1 align="center">aethelred-sdk-go</h1>

<p align="center">
  <strong>Official Go SDK for the Aethelred blockchain</strong>
</p>

<p align="center">
  <a href="https://github.com/aethelred-foundation/aethelred-sdk-go/actions/workflows/repo-security-baseline.yml"><img src="https://img.shields.io/github/actions/workflow/status/aethelred-foundation/aethelred-sdk-go/repo-security-baseline.yml?branch=main&style=flat-square&label=Security" alt="Security"></a>
  <a href="https://github.com/aethelred-foundation/aethelred-sdk-go/actions/workflows/docs-hygiene.yml"><img src="https://img.shields.io/github/actions/workflow/status/aethelred-foundation/aethelred-sdk-go/docs-hygiene.yml?branch=main&style=flat-square&label=Docs+Hygiene" alt="Docs Hygiene"></a>
  <a href="https://pkg.go.dev/github.com/aethelred-foundation/aethelred-sdk-go"><img src="https://pkg.go.dev/badge/github.com/aethelred-foundation/aethelred-sdk-go.svg" alt="Go Reference"></a>
  <a href="LICENSE"><img src="https://img.shields.io/badge/license-Apache--2.0-blue?style=flat-square" alt="License"></a>
</p>
<p align="center">
  <img src="https://img.shields.io/badge/Go-1.22+-00ADD8?style=flat-square&logo=go&logoColor=white" alt="Go">
  <img src="https://img.shields.io/badge/Cosmos_SDK-v0.50-2E3148?style=flat-square" alt="Cosmos SDK">
  <a href="https://docs.aethelred.io/sdk"><img src="https://img.shields.io/badge/docs-SDK-orange?style=flat-square" alt="Docs"></a>
</p>

---

## Install

```bash
go get github.com/aethelred-foundation/aethelred-sdk-go
```

## Quick Start

```go
package main

import (
    "context"
    "fmt"
    "log"

    aethelred "github.com/aethelred-foundation/aethelred-sdk-go"
)

func main() {
    ctx := context.Background()

    // Connect to testnet
    client, err := aethelred.NewClient("https://rpc.testnet.aethelred.io")
    if err != nil {
        log.Fatal(err)
    }
    defer client.Close()

    // Load wallet from mnemonic
    wallet, err := aethelred.WalletFromMnemonic("your twelve word mnemonic...")
    if err != nil {
        log.Fatal(err)
    }

    // Submit a compute job
    job, err := client.PoUW().SubmitJob(ctx, &aethelred.SubmitJobRequest{
        ModelHash:        []byte("abc123..."),
        InputData:        []byte(`{"prompt":"Hello AI"}`),
        VerificationType: "hybrid",
        Priority:         "standard",
        Signer:           wallet,
    })
    if err != nil {
        log.Fatal(err)
    }
    fmt.Printf("Job submitted: %s\n", job.JobID)

    // Query the resulting seal
    seal, err := client.Seal().GetSealByJob(ctx, job.JobID)
    if err != nil {
        log.Fatal(err)
    }
    fmt.Printf("Output hash: %x\n", seal.OutputHash)
    fmt.Printf("Agreement: %d/%d\n", seal.AgreementPower, seal.TotalPower)
}
```

Full API docs: [pkg.go.dev/github.com/aethelred-foundation/aethelred-sdk-go](https://pkg.go.dev/github.com/aethelred-foundation/aethelred-sdk-go)

---

## Development

```bash
go test ./...
go vet ./...
golangci-lint run
```
