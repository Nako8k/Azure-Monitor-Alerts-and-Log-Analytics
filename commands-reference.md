## CPU Stress Test & Alert Trigger

```Powershell 
while ($true) { $x = 1; 1..1000000 | % { $x *= $_ } }
```

## KQL CPU overtime

```Powershell 
Perf
| where ObjectName == "Processor"
| where CounterName == "% Processor Time"
| where Computer == "Manutaki"
| summarize avg(CounterValue) by bin(TimeGenerated, 5m), Computer
| order by TimeGenerated desc
```

## KQL Heart beat check 

```Powershell
Heartbeat
| where Computer == "Manutaki"
| order by TimeGenerated desc
| take 10
```
