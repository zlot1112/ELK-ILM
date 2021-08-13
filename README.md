# Elastic Index Lifecycle Management(ILM)
![img.png](./images/logo.png)

## 1. 인덱스 수명주기 사용이유 ?
일반적으로 로그와 데이터들은 주로 최근 데이터를 활용하여 조회하고, 잘 사용하지 않는 데이터들은 계속 쌓이면서, 리소스를 사용하게 됩니다.   
인덱스 수명주기를 사용함으로써, 리소스를 줄이고, 신속하게 데이터를 수집하고 검색하게 도와줍니다.
   
## 2. 인덱스 수명주기 4단계

* Hot : 인덱스가 업데이트되고, 활발하게 사용되는 상태      
* Warm : 인덱스가 업데이트되지 않지만, 활발하게 사용하는 상태
* Cold : the index is no longer being updated and is seldom queried. The information still needs to be searchable, but it’s okay if those queries are slower.   
* Delete : 인덱스가 이제 사용되지 않으며, 삭제해도 되는상태 

Index Lifecycle Policies 생성
Kibana > management > Index Lifecycle Policies
name : gklog-policy
Delete phase : Active , 30 Days
2. Index Template 생성
   Kibana > Dev tools

PUT _template/gklog-template
{
"index_patterns": ["gklog-*"],
"template": {
"settings": {
"number_of_shards": 1,
"number_of_replicas": 0,
"index.lifecycle.name": "gklog-policy"
}
}
}
3. Index Template <-> Index Lifecycle Policy 매핑
   kibana > management > Index Lifecycle Policies > gklog-policy > Actions > Add policy "gklog-policy" to index template
4. 기존 Index , Index Lifecycle Policy 부여
   Kibana > Dev tools
   PUT gklog-*/_settings
   {
   "index": {
   "lifecycle": {
   "name": "gklog-policy"
   }
   }
   }

2021년 06월 elasticsearch 이슈 및 해결방법 공유

##참고사이트  
- - -
[Elastic Stack Documentation](https://www.elastic.co/guide/en/elasticsearch/reference/7.0/index-lifecycle-management.html)   
[Index Lifecycle Management - ILM 기능으로 인덱스 주기 관리하기, youtube](https://youtu.be/AvR-jKBDzEo)   
[인덱스 수명 주기 관리를 통해 Hot-Warm-Cold 아키텍처 구현
](https://www.elastic.co/kr/blog/implementing-hot-warm-cold-in-elasticsearch-with-index-lifecycle-management?fbclid=IwAR1us3WdTxWdWFzoVUPeF1zXB7bdhA8GlNr1VAgCCZVO52MJ01bMvu2MnnM)   
[Elasticsearch Service를 위한 비용 절약 전략: 데이터 저장 공간 효율성
](https://www.elastic.co/kr/blog/cost-saving-strategies-for-the-elasticsearch-service-data-storage-efficiency)
