---
date : 2019-12-12
title : Hazard Condition
---


## 해저드 조건

1a. EX/MEM.RegisterRd = ID/EX.RegisterRs  
1b. EX/MEM.RegisterRd = ID/EX.RegisterRt  
2a. MEM/WB.RegisterRd = ID/EX.RegisterRs  
2b. MEM/WB.RegisterRd = ID/EX.RegisterRt  


1유형 : 뒤의 명령어에서 계산하려는 레지스터가 앞의 명령어의 소스 레지스터와 같을 때 (R유형)   
2유형 : 앞의 명령어에서 
