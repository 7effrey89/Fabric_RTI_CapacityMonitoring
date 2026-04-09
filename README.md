## 📊 Capacity Monitoring using Fabric Capacity Events

---

### Setup the eventstream:
1. Go to Real Time Hub in Fabric
2. Select Fabric Events at the left navigation menu
3. Create New Eventstream
4. Configure to select all the events as shown in below screenshot
5. Configure the eventstream to land in an eventhouse and a table called **CapacityTable**
<img width="1882" height="965" alt="image" src="https://github.com/user-attachments/assets/0f84485d-f40e-419d-98f4-120b403f2606" />


---

### KQL queries for visualizing the capacity events

---
<img width="893" height="250" alt="image" src="https://github.com/user-attachments/assets/465c4af5-2a53-4896-8ac2-4126bcab4ec3" />

### 📈 Utilization of the Capacity (%)

```kql
CapacityTable
| evaluate bag_unpack(data, columnsConflict='keep_source')
| extend capacityUnitMsBudget = baseCapacityUnits * 1000 * 30
| extend UtilizationPct = todouble(capacityUnitMs) / capacityUnitMsBudget * 100
| extend windowStartTime_gmt1 = datetime_utc_to_local(todatetime(windowStartTime), 'Europe/Berlin')
| project windowStartTime_gmt1, UtilizationPct
| where UtilizationPct < 500
| render timechart
```

---
<img width="901" height="251" alt="image" src="https://github.com/user-attachments/assets/c0857e34-033c-4ff1-9a93-12c14e3d580e" />

### ⚡ CU Consumption (CU/s every 30 seconds)

```kql
CapacityTable
| evaluate bag_unpack(data, columnsConflict='keep_source')
| extend CU_per_s = todouble(capacityUnitMs) / 1000.0 / 30.0
| extend windowStartTime_gmt1 = datetime_utc_to_local(todatetime(windowStartTime), 'Europe/Berlin')
| project windowStartTime_gmt1, CU_per_s
| render timechart
```

Note: When making RTI Dashboards the render function wont take affect. You will need to select the graphing options under "visuals" above the query result, and then "visual formatting" at the right side
<img width="1967" height="1162" alt="image" src="https://github.com/user-attachments/assets/a9164ecf-edd4-423e-8794-a9f1cfbeabd2" />
