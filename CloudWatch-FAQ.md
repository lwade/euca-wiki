# Questions
## How are the tables selected when entering metric data?
###
1. sort dimensions by key
2. append the following into a string dim1name|dim1value|dim2name|dim2value|etc. last char is a |
3. then take the sha1 in hex as the whole thing
4. hash is in the table, table chosen is first char of the hash

```import com.eucalyptus.cloudwatch.domain.DimensionEntity;
import com.eucalyptus.cloudwatch.domain.metricdata.MetricManager;
import java.util.ArrayList;
import java.util.TreeMap;

//Change values here
String[] dimensions = ["InstanceId","foo","ImageId","foo"];

TreeMap<String,String> dimMap = new TreeMap<String, String>();
// Sort (sort of)
for (int i=0;i<dimensions.length;i+=2) {
  dimMap.put(dimensions[i], dimensions[i+1]);
}

ArrayList<DimensionEntity> d = new ArrayList<DimensionEntity>();
for (String name: dimMap.keySet()) {
  String val = dimMap.get(name);
  DimensionEntity e = new DimensionEntity();
  e.setName(name);
  e.setValue(val);
  d.add(e);
}
```