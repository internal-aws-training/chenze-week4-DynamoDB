1. 创建Table: Project_NAME, 分区键为projectName，排序键为projectType。
```
aws dynamodb create-table \
  --table-name cz_user \
  --attribute-definitions file://UserTableSchema.json \
  --key-schema AttributeName=user_id,KeyType=HASH AttributeName=user_name,KeyType=RANGE \
  --provisioned-throughput ReadCapacityUnits=1,WriteCapacityUnits=1
```
2. 更新Table: 更新上表，添加Attributes: memberName, startDate。
```

```
3. 写入数据:插入5-10条数据，包含DynamoDB所有数据类型。
```
aws dynamodb put-item \
  --table-name cz_user \
  --item file://UserTableSchema.json \
  --return-consumed-capacity TOTAL

aws dynamodb batch-write-item \
  --request-items file://BatchWrite.json
```
4. 更新数据: 尝试更新已存在的数据。
```
aws dynamodb update-item \
  --table-name cz_user \
  --key file://key.json \
  --update-expression "SET #U = :u" \
  --expression-attribute-names '{"#U":"user_age"}' \
  --expression-attribute-values '{":u":{"N":  "100"}}' \
  --return-values ALL_NEW
```
5. 读取数据:根据主键读取数据。
```
aws dynamodb get-item \
  --table-name cz_user \
  --key file://key.json 
```
6. 查询数据: 根据分区键和排序键查询数据, 尝试通过memberName查询数据。
```
aws dynamodb query \
  --table-name cz_user \
  --select ALL_ATTRIBUTES \
  --key-condition-expression "user_id = :uid" \
  --filter-expression "#A <= :a" \
  --expression-attribute-names '{"#A": "user_age"}' \
  --expression-attribute-values file://Query_attribute_values.json 
```
7. 扫描数据: 全表扫描并按一定规则过滤数据(比如memberName)。
```
aws dynamodb scan \
  --table-name cz_user \
  --select ALL_ATTRIBUTES \
  --filter-expression "#A <= :a" \
  --expression-attribute-names '{"#A": "user_age"}' \
  --expression-attribute-values file://Scan_attribute_values.json 
```
8. 创建GSI:对memberName创建全局二级索引。
```
aws dynamodb update-table \
  --table-name cz_user \
  --attribute-definitions AttributeName=user_age,AttributeType=N \
  --global-secondary-index-update file://GSI.json

aws dynamodb update-table \
  --table-name cz_user \
  --global-secondary-index-update file://GsiDelete.json
```
9. 查询GSI:根据memberName查询数据。
```
aws dynamodb query \
    --table-name cz_user \
    --index-name AgeIndex \
    --key-condition-expression "user_age = :age" \
    --expression-attribute-values  '{":age":{"N":"25"}}'
```
10. 删除表: 不要着急删除表，在后续所有DynamoDB相关内容完成后删除表。
```
aws dynamodb delete-table \
  --table-name user \
```
