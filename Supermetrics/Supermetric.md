#### Use Case: User want to notify if the VM has reboot

`count(${this, metric=summary|guesttoolsRunningStatus, where=(!($value contains 'Not >Running'))}) != 0 ? (${this, metric=sys|osUptime_latest}) : 0 `

#### Use Case: Detect VM Hang

`({
‌This Resource: 
‌sys|poweredOn
} > 0.5 &&{
‌This Resource: 
‌sys|heartbeat_summation
} < 0.5 &&{
‌This Resource: 
‌cpu|usage_average
} < 1 &&{
‌This Resource: 
‌net|usage_average
} < 1 &&{
‌This Resource: 
‌storage|usage_average
} < 1) ? 1 : 0 `

#### VM CPU Contention

`
( {
‌This Resource: 
‌cpu|overlap_summation
} / {
‌This Resource: 
‌cpu|corecount_provisioned
} / 200) + ({
‌This Resource: 
‌cpu|readyPct
} + {
‌This Resource: 
‌cpu|costopPct
} + {
‌This Resource: 
‌cpu|iowaitPct
})`

#### VM Service Level Indicator
`
100 - ( {
‌This Resource: 
‌performance|number_of_kpis_breached
} * 25 )
`

#### VM Availability (%)
`
​${this,  metric=sys|poweredOn} * ceil ( min ([ max ([ ${this, metric=sys|osUptime_latest} ,${this, metric=mem|nonzero_active} ,${this, metric=virtualDisk:Aggregate of all instances|commandsAveraged_average, depth=1} ,${this, metric=net|usage_average} ]) , 1 ]) ) * min ([ 300 ,   ${this, metric=sys|osUptime_latest} ]) / 3​
`

#### [oversized VMs] Memory Recommended (KB)
`
{
‌This Resource: 
‌config|hardware|memoryKB
}-{
‌This Resource: 
‌summary|oversized|memory
}
`

#### [oversized VMs] vCPU Recommended
`
{
‌This Resource: 
‌cpu|corecount_provisioned
}-{
‌This Resource: 
‌summary|oversized|vcpus
}
`

#### [undersized VMs] Memory Recommended(KB)

`
{
‌This Resource: 
‌summary|undersized|memory
}+{
‌This Resource: 
‌mem|guest_provisioned
}
`

#### [undersized VMs] vCPU Recommended
`
{
‌This Resource: 
‌summary|undersized|vcpus
}+{
‌This Resource: 
‌cpu|corecount_provisioned
}
`