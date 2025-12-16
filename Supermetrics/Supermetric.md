#### Use Case: User want to notify if the VM has reboot

<p>count(${this, metric=summary|guesttoolsRunningStatus, where=(!($value contains 'Not >Running'))}) != 0 ? (${this, metric=sys|osUptime_latest}) : 0 </p>

#### Use Case: Detect VM Hang

<p>({
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
} < 1) ? 1 : 0 </p>

#### VM CPU Contention

<p>
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
})
</p>

#### VM Service Level Indicator
<p>
100 - ( {
‌This Resource: 
‌performance|number_of_kpis_breached
} * 25 )
</p>

#### VM Availability (%)
<p>
​${this,  metric=sys|poweredOn} * ceil ( min ([ max ([ ${this, metric=sys|osUptime_latest} ,${this, metric=mem|nonzero_active} ,${this, metric=virtualDisk:Aggregate of all instances|commandsAveraged_average, depth=1} ,${this, metric=net|usage_average} ]) , 1 ]) ) * min ([ 300 ,   ${this, metric=sys|osUptime_latest} ]) / 3​
</p>

#### [oversized VMs] Memory Recommended (KB)
<p>
{
‌This Resource: 
‌config|hardware|memoryKB
}-{
‌This Resource: 
‌summary|oversized|memory
}
</p>

#### [oversized VMs] vCPU Recommended
<p>
{
‌This Resource: 
‌cpu|corecount_provisioned
}-{
‌This Resource: 
‌summary|oversized|vcpus
}
</p>

#### [undersized VMs] Memory Recommended(KB)

<p>
{
‌This Resource: 
‌summary|undersized|memory
}+{
‌This Resource: 
‌mem|guest_provisioned
}
</p>

#### [undersized VMs] vCPU Recommended
<p>
{
‌This Resource: 
‌summary|undersized|vcpus
}+{
‌This Resource: 
‌cpu|corecount_provisioned
}
</p>

Reference: https://blogs.vmware.com/cloud-foundation/2020/09/15/my-top-15-vrealize-operations-super-metrics/