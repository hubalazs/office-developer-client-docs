---
title: "Field.GetChunk Method (DAO)"
 
 
manager: soliver
ms.date: 3/9/2015
ms.audience: Developer
ms.topic: reference
f1_keywords:
- dao360.chm1052871
  
localization_priority: Normal
ms.assetid: b8984e79-54f7-8052-85a3-d12033daf7a1
description: "Returns all or a portion of the contents of a Memo or Long BinaryField object in the Fields collection of a Recordset object."
---

# Field.GetChunk Method (DAO)

Returns all or a portion of the contents of a **Memo** or **Long Binary** **[Field](field-object-dao.md)** object in the **[Fields](fields-collection-dao.md)** collection of a **[Recordset](recordset-object-dao.md)** object. 
  
## Syntax

 *expression*  . **GetChunk**( ** *Offset* **, ** *Bytes* ** ) 
  
 *expression*  A variable that represents a **Field** object. 
  
### Parameters

|**Name**|**Required/Optional**|**Data Type**|**Description**|
|:-----|:-----|:-----|:-----|
| _Offset_ <br/> |Required  <br/> |**Long** <br/> |The number of bytes to skip before copying begins.  <br/> |
| _Bytes_ <br/> |Required  <br/> |**Long** <br/> |The number of bytes you want to return.  <br/> |
   
### Return Value

Variant
  
## Remarks

The bytes returned by **GetChunk** are assigned to variable. Use **GetChunk** to return a portion of the total data value at a time. You can use the **[AppendChunk](field-appendchunk-method-dao.md)** method to reassemble the pieces. 
  
If  _offset_ is 0, **GetChunk** begins copying from the first byte of the field. 
  
If  _numbytes_ is greater than the number of bytes in the field, **GetChunk** returns the actual number of remaining bytes in the field. 
  
> [!NOTE]
> Use a **Memo** field for text, and put binary data only in **Long Binary** fields. Doing otherwise will cause undesirable results. 
  
## Example

This example uses the **AppendChunk** and **GetChunk** methods to fill an OLE object field with data from another record, 32K at a time. In a real application, one might use a procedure like this to copy an employee record (including the employee's photo) from one table to another. In this example, the record is simply being copied back to same table. Note that all the chunk manipulation takes place within a single AddNew-Update sequence. 
  
```
Sub AppendChunkX() 
 
 Dim dbsNorthwind As Database 
 Dim rstEmployees As Recordset 
 Dim rstEmployees2 As Recordset 
 
 Set dbsNorthwind = OpenDatabase("Northwind.mdb") 
 
 ' Open two recordsets from the Employees table. 
 Set rstEmployees = _ 
 dbsNorthwind.OpenRecordset("Employees", _ 
 dbOpenDynaset) 
 Set rstEmployees2 = rstEmployees.Clone 
 
 ' Add a new record to the first Recordset and copy the 
 ' data from a record in the second Recordset. 
 With rstEmployees 
 .AddNew 
 !FirstName = rstEmployees2!FirstName 
 !LastName = rstEmployees2!LastName 
 CopyLargeField rstEmployees2!Photo, !Photo 
 .Update 
 
 ' Delete new record because this is a demonstration. 
 .Bookmark = .LastModified 
 .Delete 
 .Close 
 End With 
 
 rstEmployees2.Close 
 dbsNorthwind.Close 
 
End Sub 
 
Function CopyLargeField(fldSource As Field, _ 
 fldDestination As Field) 
 
 ' Set size of chunk in bytes. 
 Const conChunkSize = 32768 
 
 Dim lngOffset As Long 
 Dim lngTotalSize As Long 
 Dim strChunk As String 
 
 ' Copy the photo from one Recordset to the other in 32K 
 ' chunks until the entire field is copied. 
 lngTotalSize = fldSource.FieldSize 
 Do While lngOffset < lngTotalSize 
 strChunk = fldSource.GetChunk(lngOffset, conChunkSize) 
 fldDestination.AppendChunk strChunk 
 lngOffset = lngOffset + conChunkSize 
 Loop 
 
End Function 

```

