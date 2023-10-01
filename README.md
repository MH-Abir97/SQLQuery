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

```
 Select UDD.EmpName, B.CreatedBy from MERC_KinttingSheetMaster B LEFT outer JOIN(
						select V.EmpName,V.Designation,US.ID UIDs,V.Email from ADM_User US INNER JOIN vw_All_EmployeeInformation V ON V.EmpId=US.EmpId) UDD 
						ON UDD.UIDs=B.CreatedBy 
```               

```
select V.EmpName,V.Designation,US.ID UIDs,V.Email from ADM_User US INNER JOIn vw_All_EmployeeInformation V ON V.EmpId=US.EmpId   
```



# Diffrent Type Join

```
                        select A.*,CT.Line,CT.Date,ISNULL(CT.Production,0) Production,CT.ID
                        from (
								SELECT A.ID ColorID,M.JobNo,BR.Name BuyerName,A.StyleNumber, A.StyleName,ColorCode, ColorName,ColorOrderqty
								,'' ItemName,M.ShipDate,M.OrderNo
								FROM MERC_OrderBookingMaster M,MERC_OrderBookingDetails A,MERC_BuyerInformation BR 
								where  M.ID=A.RefId and BR.ID=M.BuyerId 
                        ) A left outer join (select CT.ID,CT.ColorId,CT.Date,CT.Production,LN.Line from Prd_DailyCarton CT,HR_Line LN where LN.ID=CT.LineId) 
                        CT on CT.ColorId=A.ColorID  
```


# Multiple Comma separator Join

```
SELECT
    PCBM.ID AS BatchID,
    PCBM.BatchNo,
    PCBM.AreaId,
    PCBM.StoreId,
    PCBM.BatchDate,
    PCBM.BatchStatus,
    PCBM.Currency,
    PCBM.ConvRate,
    PCBM.CreatedBy,
    PCBM.CreatedTime,
    PCBM.AskPriceLastDate,
    PCBM.ApRefId,
    PCBM.ApStatus,
    PCBM.CompanyId,
    PCBM.SupplierIds,
    PCBM.Remarks,
    PS.SupplierName,  -- Replace with the actual column name for the supplier name in your PUR_Supplier table
    PS.SupplierAddress -- Replace with the actual column name for the supplier address in your PUR_Supplier table
FROM Pur_CsBatchMaster PCBM
LEFT JOIN PUR_Supplier PS
    ON PCBM.SupplierIds LIKE CONCAT('%', PS.SupplierID, '%')
WHERE PCBM.BatchStatus = 'Approved';
```
# Multiple Comma separator string Split

```
SELECT CSM.ID, CSM.BatchNo, CSM.AreaId,
CSM.StoreId,
CSM.BatchDate,
CSM.BatchStatus,
CSM.Currency,
CSM.ConvRate,
CSM.CreatedBy, 
CSM.CreatedTime,
CSM.AskPriceLastDate,
CSM.ApRefId,
CSM.ApStatus,
CSM.CompanyId,
CSM.SupplierIds,
CSM.Remarks,
CSM.DocFor 
, (select StoreName from INV_StoreName x where x.ID = CSM.StoreId) StoreName
, (select Name from HR_Orgination x where x.ID = CSM.CompanyId) CompanyName
, CASE WHEN ISNULL(CSM.SupplierIds, '0') = '0' THEN '' ELSE STUFF((SELECT '; ' + sn.SupplierName FROM PUR_Supplier sn
WHERE sn.ID in ( SELECT value FROM STRING_SPLIT(CSM.SupplierIds, ',') ) FOR XML PATH('')), 1, 1, '') END AS SupplierName
FROM Pur_CsBatchMaster CSM
```

```

# Gendral Query
Select a.ID BuyerMasterId, b.ID SCId, b.Beneficiary BeneficiaryId,c.Name BeneficiaryName,b.BuyerName BuyerId,
            d.Name BuyerName,b.AmenSL SCNO,b.Currency CurrencyId,e.Currency CurrencyName,b.LineBank LineBankId,
            f.BankName LineBankName, 
			a.DocSubmitToBuyerNoSys DocSubmitToBuyerNoSys,a.SubmissionDate
			,g.Price,
			h.BLCargoAWBNo,h.BLCargoAWBDate,h.InvoiceNo,h.InvoiceDate,
			i.JobNo
			from Comm_Export_DocSubmitToBuyerMaster a
            left join Comm_Export_SalesContract b ON a.SCId=b.ID
            left join HR_Orgination c ON a.Beneficiary = c.ID
            left join MERC_BuyerInformation d ON a.BuyerName = d.ID
            left join Comm_Currency e ON b.Currency = e.ID
            left join Comm_Bank f ON b.LineBank=f.ID
            left join (
            Select RefId,Sum(CAST(isnull(InvoiceValue,0) AS DECIMAL(18, 4))) Price 
            from Comm_Export_DocSubmitToBuyerDetails
            Group by RefId
            ) g ON g.RefId = a.ID
			left join (
			Select a.RefId,STRING_AGG(b.BLCargoAWBNo,', ') as BLCargoAWBNo,
			STRING_AGG(b.BLCargoAWBDate,', ') as BLCargoAWBDate
			,STRING_AGG(b.InvoiceNo,', ') as InvoiceNo,STRING_AGG(b.InvoiceDate,', ') as InvoiceDate
			from Comm_Export_DocSubmitToBuyerDetails a 
			left join Comm_Export_ExportInvoiceMaster b ON a.invoiceId=b.ID
			group by a.RefId
			)h ON h.RefId=a.ID
			left join (
			Select a.RefId,STRING_AGG(c.JobNo,', ') as JobNo   
			from Comm_Export_DocSubmitToBuyerDetails a 
			left join Comm_Export_ExportInvoiceMaster b ON a.invoiceId=b.ID
			left join Comm_Export_ExportInvoiceDtl c ON b.ID = c.RefId
			where CAST(isnull(c.POQty,0) AS int) > 0 
			group by a.RefId
			) i ON i.RefId=a.ID
            Where 1=1   And 
			a.ID IN ('7DB4FD22-63D5-4AA5-B81C-DAAF4DFBC18C','4618736F-5A42-4CCC-8AD7-EC6B05BBBA44') 
			order by CONVERT(DATETIME, a.SubmissionDate,103)

			  Select RefId,Sum(CAST(isnull(InvoiceValue,0) AS DECIMAL(18, 4))) Price 
            from Comm_Export_DocSubmitToBuyerDetails
            Group by RefId
			
            Select a.ID BuyerMasterId, b.ID SCId, b.Beneficiary BeneficiaryId,c.Name BeneficiaryName,b.BuyerName BuyerId,
            d.Name BuyerName,b.AmenSL SCNO,b.Currency CurrencyId,e.Currency CurrencyName,b.LineBank LineBankId,
            f.BankName LineBankName, 
			a.DocSubmitToBuyerNoSys DocSubmitToBuyerNoSys,a.SubmissionDate
			--,g.Price,
			--h.BLCargoAWBNo,h.BLCargoAWBDate,h.InvoiceNo,h.InvoiceDate,
			--i.JobNo
			,i.Price
			,i.RefId
			,j.JobNo
			from Comm_Export_DocSubmitToBuyerMaster a
            left join Comm_Export_SalesContract b ON a.SCId=b.ID
            left join HR_Orgination c ON a.Beneficiary = c.ID
            left join MERC_BuyerInformation d ON a.BuyerName = d.ID
            left join Comm_Currency e ON b.Currency = e.ID
            left join Comm_Bank f ON b.LineBank=f.ID
			left join (SELECT CDD.RefId,Sum(CAST(isnull(InvoiceValue,0) AS DECIMAL(18, 4))) Price
			FROM Comm_Export_DocSubmitToBuyerDetails CDD Group BY CDD.RefId)i  ON i.RefId=a.ID
			LEFT JOIN (
			SELECT a.RefId,STRING_AGG(c.JobNo,',') as JobNo    FROM  Comm_Export_DocSubmitToBuyerDetails a 
			LEFT JOIN Comm_Export_ExportInvoiceMaster b ON a.invoiceId=b.ID
			LEFT JOIN Comm_Export_ExportInvoiceDtl c    ON c.RefId=b.ID
			WHERE CAST(ISNULL(c.POQty,0) AS INT) > 0
			GROUP BY a.RefId ) j ON j.RefId=a.ID


# TempTable Query
----- Create Price TempTable ====>>

Create Table #TempPrice(RefId uniqueidentifier, Price DECIMAL(18, 4))
INSERT INTO  #TempPrice (RefId,Price)
SELECT CDD.RefId, SUM(CAST(ISNULL(InvoiceValue, 0) AS DECIMAL(18, 4)))
FROM Comm_Export_DocSubmitToBuyerDetails CDD
GROUP BY CDD.RefId;

----Create Job Table Create ========>>>
Create Table #TempJobTable(RefId uniqueidentifier, JobNo NVARCHAR(MAX))
INSERT INTO  #TempJobTable(RefId,JobNo)
SELECT a.RefId, STRING_AGG(c.JobNo, ',') as JobNo
FROM Comm_Export_DocSubmitToBuyerDetails a
LEFT JOIN Comm_Export_ExportInvoiceMaster b ON a.invoiceId = b.ID
LEFT JOIN Comm_Export_ExportInvoiceDtl c ON c.RefId = b.ID
WHERE CAST(ISNULL(c.POQty, 0) AS INT) > 0
GROUP BY a.RefId;
SELECT
    a.ID AS BuyerMasterId,
    b.ID AS SCId,
    b.Beneficiary AS BeneficiaryId,
    c.Name AS BeneficiaryName,
    b.BuyerName AS BuyerId,
    d.Name AS BuyerName,
    b.AmenSL AS SCNO,
    b.Currency AS CurrencyId,
    e.Currency AS CurrencyName,
    b.LineBank AS LineBankId,
    f.BankName AS LineBankName,
    a.DocSubmitToBuyerNoSys AS DocSubmitToBuyerNoSys,
    a.SubmissionDate,
    p.Price,
	p.RefId,
	j.JobNo

FROM
    Comm_Export_DocSubmitToBuyerMaster a
LEFT JOIN
    Comm_Export_SalesContract b ON a.SCId = b.ID
LEFT JOIN
    HR_Orgination c ON a.Beneficiary = c.ID
LEFT JOIN
    MERC_BuyerInformation d ON a.BuyerName = d.ID
LEFT JOIN
    Comm_Currency e ON b.Currency = e.ID
LEFT JOIN
    Comm_Bank f ON b.LineBank = f.ID
LEFT JOIN #TempPrice P ON P.RefId=a.ID
LEFT JOIN #TempJobTable J ON J.RefId=a.ID
DROP TABLE #TempPrice;
DROP TABLE #TempJobTable;




# With Query
WITH CTE_Price AS (
    SELECT RefId, SUM(CAST(ISNULL(InvoiceValue, 0) AS DECIMAL(18, 4))) AS Price
    FROM Comm_Export_DocSubmitToBuyerDetails
    GROUP BY RefId
),
CTE_JOB AS (
 SELECT a.RefId, STRING_AGG(c.JobNo, ', ') AS JobNo
    FROM Comm_Export_DocSubmitToBuyerDetails a
    LEFT JOIN Comm_Export_ExportInvoiceMaster b ON a.invoiceId = b.ID
    LEFT JOIN Comm_Export_ExportInvoiceDtl c ON c.RefId = b.ID
    WHERE CAST(ISNULL(c.POQty, 0) AS INT) > 0
    GROUP BY a.RefId
)
SELECT
    a.ID AS BuyerMasterId,
    b.ID AS SCId,
    b.Beneficiary AS BeneficiaryId,
    c.Name AS BeneficiaryName,
    b.BuyerName AS BuyerId,
    d.Name AS BuyerName,
    b.AmenSL AS SCNO,
    b.Currency AS CurrencyId,
    e.Currency AS CurrencyName,
    b.LineBank AS LineBankId,
    f.BankName AS LineBankName,
    a.DocSubmitToBuyerNoSys AS DocSubmitToBuyerNoSys,
    a.SubmissionDate AS SubmissionDate,
    g.Price AS Price,
    i.JobNo AS JobNo
FROM Comm_Export_DocSubmitToBuyerMaster a
LEFT JOIN Comm_Export_SalesContract b ON a.SCId = b.ID
LEFT JOIN HR_Orgination c ON a.Beneficiary = c.ID
LEFT JOIN MERC_BuyerInformation d ON a.BuyerName = d.ID
LEFT JOIN Comm_Currency e ON b.Currency = e.ID
LEFT JOIN Comm_Bank f ON b.LineBank = f.ID
LEFT JOIN CTE_Price g ON g.RefId = a.ID
LEFT JOIN CTE_JOB i ON i.RefId = a.ID
WHERE a.ID IN ('7DB4FD22-63D5-4AA5-B81C-DAAF4DFBC18C', '4618736F-5A42-4CCC-8AD7-EC6B05BBBA44')
ORDER BY CONVERT(DATETIME, a.SubmissionDate, 103);
		  
```
						   
                           
