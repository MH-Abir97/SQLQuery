# All Number Concate in Single Column

```
Select STUFF((SELECT ',' + CAST(rm.ReqNum AS varchar) from SSP_Purchase_RequestPI rb 
		                Left Join SSP_Purchase_Request pr On pr.ID = rb.PurcReqID
		                Left Join SSP_ReqPrimeDetails rd On rd.ID = pr.ReqDetId
		                Left Join SSP_ReqPrimeMaster rm On rm.ID = rd.ReqID
						 FOR XML PATH('')) ,1,1,'') as ReqNums
```


# Left Join Inside Query

```

Select a.ID MERCOrderBookingMasterID
,b.ID MERCOrderBookingDetailsID
,a.JobNo
,b.StylePoNo PONo
,b.StyleNumber StyleNo
,b.ColorName,ISNULL(b.ColorOrderqty,0) AppQty
,b.UnitPriceDet Rate
,a.ShipDate
,c.ItemName,
ISNULL(b.ColorOrderqty,0) - ISNULL(d.POQty,0) BalanceQty,b.StyleOptionID
from MERC_OrderBookingMaster a 
left join MERC_OrderBookingDetails b ON a.ID = b.RefId
left join (
    select distinct A.ID,A.JobNo,D.Name ItemName from MERC_OrderBookingMaster A
	inner join MERC_Query_StyleOption B on B.QueryId=A.QueryID
	inner join MERC_NewStyle D on D.ID=B.ItemId
) c ON a.ID=c.ID
left join (
Select MERCOrderBookingDetailsID, sum(CONVERT(decimal(12, 0),isnull(POQty,0))) POQty 
from Comm_Export_SalesContractDtl
group by MERCOrderBookingDetailsID
)d ON b.ID=d.MERCOrderBookingDetailsID
where a.isSubCon is null 
AND a.JobNo > 0 
AND b.ID is not null
AND ISNULL(b.ColorOrderqty,0) > ISNULL(d.POQty,0) 
```

# Iner join OR Where Condition
```
SELECT A.ID
,JobNo
,BuyingHouseID
,BuyerId
FROM MERC_OrderBookingMaster A,MERC_DyeingBookingMaster B where A.ID=B.OrderId 
```
# SQL SELECT Query inside Join

```
                select * from (select A.*,B.Name BuyerName,C.QueryID QueryNo,D.Name SupplierName from Merc_FabricBookingMaster A
	                left outer Join MERC_BuyerInformation B on A.BuyerId=B.ID
	                left outer Join MERC_QuerySheetMaster C on A.QueryId=C.Id
	                left outer Join MERC_Supplier D on D.ID=A.SupplierId
                )x where 1=1 
```
						
# INNER JOIN AND WHERE Condition 

                               ```              Select UDD.EmpName, B.CreatedBy from MERC_KinttingSheetMaster B LEFT outer JOIN(
						select V.EmpName,V.Designation,US.ID UIDs,V.Email from ADM_User US INNER JOIN vw_All_EmployeeInformation V ON V.EmpId=US.EmpId) UDD 
						ON UDD.UIDs=B.CreatedBy 


						select V.EmpName,V.Designation,US.ID UIDs,V.Email from ADM_User US INNER JOIn vw_All_EmployeeInformation V ON V.EmpId=US.EmpId      
                                 ```
