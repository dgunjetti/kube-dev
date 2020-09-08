iptables/proxier.go

* Data structures

Proxier is an iptables based proxy for connections between a localhost:lport
and services that provide the actual backends.

Code flow:

- Scenario: Create a service 

- client_go,shared informer -> ResourceEventHandlerFuncs.OnAdd -> ServiceConfig.handleAddService() OnServiceAdd() -> onServiceUpdate() -> ServiceChangeTracker.Update()


- another goroutine
async/BoundedFrequencyRunner

tryrun -> syncProxyRules 


updates given service's change map
reflect.DeepEqual()
-> Run() - push empty struct to run channel, avoid blocking.

```go
	select {
	case bfr.run <- struct{}{}:
	default:
	}
```
bounded_frequency_runner.Loop() is running as part of a goroutine waiting for run requests.

Proxier.syncProxyRules() 

- iptables-save/restore

metric stuff for function
```go
start := time.Now()
defer func() {
    metrics.SyncProxyRulesLatency.Observe(metrics.SinceInSeconds(start))
    metrics.DeprecatedSyncProxyRulesLatency.Observe(metrics.SinceInMicroseconds(start))
    klog.V(4).Infof("syncProxyRules took %v", time.Since(start))
}()
```

- update service map, endpoints map

- sync iptable rules

- loop over jumpchains

- there is table and chain

- get iptables-save output to check for existing chains and rules.

- utiliptables 

- get existingFilterChainsData rules.

```go
cmd := runner.exec.Command(iptablesSaveCmd, args...)
err := cmd.Run()
```

- proxier.iptablesData.Write()

- runner.restoreInternal() -> cmd.CombinedOutput() -> (*osexec.Cmd)(cmd).CombinedOutput()



