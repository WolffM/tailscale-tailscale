## Steps to reproduce
1. From the repository root, run a baseline package check: `go test ./ssh/tailssh`.
2. Run the dedicated integration repro test with the integration build tag: `go test -tags=integrationtest ./ssh/tailssh -run TestX11ForwardingRequest -count=1`.
3. The test opens a session channel and sends an SSH `x11-req` request with a standard MIT-MAGIC-COOKIE payload, matching the SSH X11 forwarding handshake stage.

## Observed
The repro test fails immediately because the server rejects the `x11-req` channel request. Actual output:
`--- FAIL: TestX11ForwardingRequest`
`tailssh_integration_test.go:425: x11-req request was rejected`
`FAIL tailscale.com/ssh/tailssh`
This corresponds to end-user behavior like `X11 forwarding request failed on channel 0` when using `ssh -X` against tailscale ssh.

## Expected
The SSH session channel should accept `x11-req` when X11 forwarding is requested, so a client using `ssh -X host` can proceed without the forwarding request being denied at channel setup time. The repro test should pass once tailscale ssh supports this request path correctly.
