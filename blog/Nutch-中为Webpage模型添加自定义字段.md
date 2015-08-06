title: "Nutch 中为Webpage模型添加自定义字段"
date: 2015-07-31 16:33:18
tags: nutch
---

近来一直在研究nutch，今天想在默认的webpage表中增加几个自定义字段，折腾了一天，终于加入成功了。
我的环境是win7 + Nutch2.2.1 + mysql5.6.12。

一句话，新增自定义字段主要需要修改两个文件：**webpage.java**和**gora-sql-mapping**.xml（这里我使用的数据库是mysql，如果是hbase的话修改gora-hbase-mapping.xml文件）。

第一，找到webpage.class在webpage中的_SCHEMA Field以及新字段对应的属性做相应的修改。

``` java
public static final Schema _SCHEMA = Schema
        .parse("{\"type\":\"record\",\"name\":\"WebPage\",\"namespace\":\"org.apache.nutch.storage\",\"fields\":[{\e\":\"baseUrl\",\"type\":\"string\"},{\"name\":\"status\",\"type\":\"int\"},{\"name\":\"fetchTime\",\"type\long\"},{\"name\":\"prevFetchTime\",\"type\":\"long\"},{\"name\":\"fetchInterval\",\"type\":\"int\"},{\"nam\"retriesSinceFetch\",\"type\":\"int\"},{\"name\":\"modifiedTime\",\"type\":\"long\"},{\"name\":\"prevModifime\",\"type\":\"long\"},{\"name\":\"protocolStatus\",\"type\":{\"type\":\"record\",\"name\":\"ProtocolStat,\"fields\":[{\"name\":\"code\",\"type\":\"int\"},{\"name\":\"args\",\"type\":{\"type\":\"array\",\"items\"tring\"}},{\"name\":\"lastModified\",\"type\":\"long\"}]}},{\"name\":\"content\",\"type\":\"bytes\"},{\"nam\"contentType\",\"type\":\"string\"},{\"name\":\"prevSignature\",\"type\":\"bytes\"},{\"name\":\"signature\type\":\"bytes\"},{\"name\":\"title\",\"type\":\"string\"},{\"name\":\"text\",\"type\":\"string\"},{\"name\parseStatus\",\"type\":{\"type\":\"record\",\"name\":\"ParseStatus\",\"fields\":[{\"name\":\"majorCode\",\"\":\"int\"},{\"name\":\"minorCode\",\"type\":\"int\"},{\"name\":\"args\",\"type\":{\"type\":\"array\",\"ite:\"string\"}}]}},{\"name\":\"score\",\"type\":\"float\"},{\"name\":\"reprUrl\",\"type\":\"string\"},{\"name"headers\",\"type\":{\"type\":\"map\",\"values\":\"string\"}},{\"name\":\"outlinks\",\"type\":{\"type\":\"m,\"values\":\"string\"}},{\"name\":\"inlinks\",\"type\":{\"type\":\"map\",\"values\":\"string\"}},{\"name\"arkers\",\"type\":{\"type\":\"map\",\"values\":\"string\"}},{\"name\":\"metadata\",\"type\":{\"type\":\"map"values\":\"bytes\"}},{\"name\":\"batchId\",\"type\":\"string\"},{\"name\":\"body\",\"type\":\"bytes\{\"name\":\"ISSN\",\"type\":\"string\"}]}
public static enum Field {
    BASE_URL(0, "baseUrl"), STATUS(1, "status"), FETCH_TIME(2, "fetchTime"), PREV_FETCH_TIME(
            3, "prevFetchTime"), FETCH_INTERVAL(4, "fetchInterval"), RETRIES_SINCE_FETCH(
            5, "retriesSinceFetch"), MODIFIED_TIME(6, "modifiedTime"), PREV_MODIFIED_TIME(
            7, "prevModifiedTime"), PROTOCOL_STATUS(8, "protocolStatus"), CONTENT(
            9, "content"), CONTENT_TYPE(10, "contentType"), PREV_SIGNATURE(
            11, "prevSignature"), SIGNATURE(12, "signature"), TITLE(13,
            "title"), TEXT(14, "text"), PARSE_STATUS(15, "parseStatus"), SCORE(
            16, "score"), REPR_URL(17, "reprUrl"), HEADERS(18, "headers"), OUTLINKS(
            19, "outlinks"), INLINKS(20, "inlinks"), MARKERS(21, "markers"), METADATA(
            22, "metadata"), BATCH_ID(23, "batchId"), BODY(24, "body"), ISSN(25, "ISSN");
    private int index;
    private String na
    Field(int index, String name) {
        this.index = index;
        this.name = name;
  
    public int getIndex() {
        return index;
  
    public String getName() {
        return name;
  
    public String toString() {
        return name;
    }

public static final String[] _ALL_FIELDS = { "baseUrl", "status",
        "fetchTime", "prevFetchTime", "fetchInterval", "retriesSinceFetch",
        "modifiedTime", "prevModifiedTime", "protocolStatus", "content",
        "contentType", "prevSignature", "signature", "title", "text",
        "parseStatus", "score", "reprUrl", "headers", "outlinks",
        "inlinks", "markers", "metadata", "batchId", "body", "issn" };
static {
    PersistentBase.registerFields(WebPage.class, _ALL_FIELDS);
}

private Utf8 body;
private String ISSN;

public Utf8 getBody() {
    return (Utf8) get(24);
}
public void setBody(Utf8 value) {
    put(24, value);
}

public String getISSN() {
    return (String) get(25);
}
public void setISSN(String value) {
    put(25, value);
}
```
以及Object get()和set()中添加新字段的相关信息。

第二 在gora-sql-mapping文件中添加想要增加的字段的Mapping关系，如下

``` xml
<field name="body" column="body" length="32000" />
<field name="ISSN" column="ISSN" length="10" />
```

以上就可以直接运行nutch了，如果有报错信息的话可以调试查看gora-sql-0.2.jar中的SqlStore.java中的createSchema()方法，这个方法中会生成有建表语句，根据错误信息做相应的修改即可。

```java
@Override
public void createSchema() throws IOException {
  if(!schemaExists()) {
    log.info("creating schema: " + sqlTable.getAbsoluteName());
    CreateTableQuery query = new CreateTableQuery(sqlTable, true);
    setColumnConstraintForQuery(query, primaryColumn);
    for(Column column : mapping.getFields().values()) {
      setColumnConstraintForQuery(query, column);
    }
    PreparedStatement statement = null;
    try {
      statement = connection.prepareStatement(query.validate().toString());
      statement.executeUpdate();
    } catch (SQLException ex) {
      throw new IOException(ex);
    } finally {
      SqlUtils.close(statement);
    }
  }
}
```

EOF