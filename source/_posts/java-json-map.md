title: json与map互转demo
date: 2016.01.13
categories: java #分类
tags: [java, map, json]
toc: true
---

## 依赖包
``` bash
<dependency>
    <groupId>com.fasterxml.jackson.core</groupId>
    <artifactId>jackson-databind</artifactId>
    <version>2.5.3</version>
  </dependency>
  </dependencies>
```

## map ==> json
``` bash
public class JsonJacksonMapMappingDemo {

  public static void main(String args[]) {
    ObjectMapper mapper = new ObjectMapper();

    Map<String, Object> carMap = new HashMap<String, Object>();

    carMap.put("car", "Audi");
    carMap.put("price", "30000");
    carMap.put("model", "2010");

    List<String> colors = new ArrayList<String>();
    colors.add("Grey");
    colors.add("White");
    colors.add("Black");

    carMap.put("colors", colors);

    /**
     * Convert Map to JSON and write to a file
     */
    try {
      mapper.writeValue(new File("result.json"), carMap);
    } catch (Exception e) {
      e.printStackTrace();
    }

  }
}
```
## json ==> map
``` bash
public class JsonJacksonMapMappingDemo {

  public static void main(String args[]) {
    ObjectMapper mapper = new ObjectMapper();


    /**
     * Read JSON from a file into a Map
     */
    try {
      Map<String, Object> carMap = mapper.readValue(new File(
          "result.json"), new TypeReference<Map<String, Object>>() {
      });

      System.out.println("Car : " + carMap.get("car"));
      System.out.println("Price : " + carMap.get("price"));
      System.out.println("Model : " + carMap.get("model"));
      System.out.println("Colors : " + carMap.get("colors"));

    } catch (Exception e) {
      e.printStackTrace();
    }

  }
}
```