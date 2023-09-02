# All Number Concate in Single Column

```
Select STUFF((SELECT ',' + CAST(rm.ReqNum AS varchar) from SSP_Purchase_RequestPI rb 
		                Left Join SSP_Purchase_Request pr On pr.ID = rb.PurcReqID
		                Left Join SSP_ReqPrimeDetails rd On rd.ID = pr.ReqDetId
		                Left Join SSP_ReqPrimeMaster rm On rm.ID = rd.ReqID
						 FOR XML PATH('')) ,1,1,'') as ReqNums
```
