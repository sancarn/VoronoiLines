# Voronoi Lines V2 - Resolution Method

This method takes a user specified number - resolution. This number is the 'number of points to be created per line'. I.E. With a resolution of 5, 5 points will be created for each line (not poly line) of the base layer.

This allows us to, theoretically, increase the resolution to get better and better results.

## Method

The algorithm is a fairly simple extension of the previous example. Let's look at the main loop again:

```
'Create point objects from lines table, a point will be created at every vertex/node of the line.
Set CoordSys table ~TempLinesTable
Dim iRow, pNum, nNum, thisNumber as integer, thisObject as object
for iRow = 1 to Tableinfo(~TempLinesTable,8)
	fetch rec iRow from ~TempLinesTable
	thisObject = ~TempLinesTable.obj
	thisNumber = ~TempLinesTable.NumberID 
	for pNum = 1 to ObjectInfo(thisObject,21)
		for nNum = 1 to ObjectInfo(thisObject,20)
			'INSERT POINT INTO VoronoiPoints
			insert into VoronoiPoints (obj, NumberID) values (CreatePoint(ObjectNodeX(thisObject, pNum, nNum),ObjectNodeY(thisObject, pNum, nNum)), thisNumber)
		next
	next
next
```

We simple extend this algorithm so we are no longer looking at individual points and instead looking at lines:


```
'Create point objects from lines table, a point will be created at every vertex/node of the line.
Set CoordSys table ~TempLinesTable
Dim iRow, pNum, nNum, thisNumber as integer, thisObject as object, X1, X2, Y1, Y2 as float
Dim resolution as integer
resolution = 3
for iRow = 1 to Tableinfo(~TempLinesTable,8)
    fetch rec iRow from ~TempLinesTable
    thisObject = ~TempLinesTable.obj
    thisNumber = ~TempLinesTable.NumberID 
    for pNum = 1 to ObjectInfo(thisObject,21)
        for nNum = 1 to ObjectInfo(thisObject,20) - 1
            'Get start and end locations
			X1 = ObjectNodeX(thisObject, pNum, nNum)
			Y1 = ObjectNodeY(thisObject, pNum, nNum)
			X2 = ObjectNodeX(thisObject, pNum, nNum+1)
			Y2 = ObjectNodeY(thisObject, pNum, nNum+1)
			
			'Insert line into VoronoiPoints
			Call createLinePoints(X1,Y1,X2,Y2,resolution,"VoronoiPoints",thisNumber)
        next
    next
next

Sub createLinePoints(X1 as float,Y1 as float,X2 as float,Y2 as float,byval resolution as integer, byval insertTo as string, NumberID as integer)
  Dim dx,dy,tx,ty as float, i as integer
  dx = X2-X1
  dy = Y2-Y1
  for i = 0 to resolution-1
    tx = X1 + dx * i/(resolution-1)
    ty = Y1 + dy * i/(resolution-1)
    insert into insertTo (obj,NumBerID) values (CreatePoint(tx,ty),NumberID)
  next
End Sub
```

Ultimately we do exactly the same as before however now we pass the line to a function which creates points along the line.

# Results
