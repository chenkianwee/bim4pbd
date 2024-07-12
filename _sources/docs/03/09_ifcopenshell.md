# IFCOpenShell
## IfcRelAssignsToGroup
- https://standards.buildingsmart.org/IFC/RELEASE/IFC4_3/HTML/lexical/IfcZone.htm
- https://standards.buildingsmart.org/IFC/RELEASE/IFC4_3/HTML/lexical/IfcRelAssignsToGroup.htm
- https://standards.buildingsmart.org/IFC/RELEASE/IFC4_3/HTML/annex_e/structural-analysis-model/structural-curve-member.html

## IFCTimeSeries
```
import ifcopenshell
import ifcopenshell.api
# region: IFCTimeseries
ifcmodel = ifcopenshell.file()

irregular_vals = []
ifc_dt = '2024-06-01T12:10:00'
ifc_real = ifcmodel.createIfcReal(2.3)
irregular_val = ifcmodel.createIfcIrregularTimeSeriesValue()
irregular_val.TimeStamp = ifc_dt
irregular_val.ListValues = [ifc_real, ifc_real]
irregular_vals.append(irregular_val)

ifc_dt = '2024-06-01T12:18:00'
ifc_real = ifcmodel.createIfcReal(10.2)
irregular_val = ifcmodel.createIfcIrregularTimeSeriesValue()
irregular_val.TimeStamp = ifc_dt
irregular_val.ListValues = [ifc_real]
irregular_vals.append(irregular_val)

print(irregular_vals)
ifc_irregular_time_series = ifcmodel.create_entity('IfcIrregularTimeSeries', Name = 'my time series', StartTime = '2024-06-01T12:00:00',
                                                   EndTime = '2024-06-01T12:00:00', TimeSeriesDataType='DISCRETE', DataOrigin='MEASURED', 
                                                   Values=irregular_vals)
print(ifc_irregular_time_series)

ifc_template = ifcopenshell.api.run("pset_template.add_pset_template", ifcmodel, name='ifctimeseries_template', template_type='PSET_PERFORMANCEDRIVEN')
ifcopenshell.api.run("pset_template.add_prop_template", ifcmodel, pset_template=ifc_template, name='timeseries_prop', 
                     template_type='P_REFERENCEVALUE', primary_measure_type='IfcTimeSeries')

ifcmodel.write('/home/Desktop/test.ifc')
# endregion: IFCTimeseries
```