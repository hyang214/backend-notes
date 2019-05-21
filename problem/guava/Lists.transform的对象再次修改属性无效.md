## Lists.tranform的对象再次修改属性无效

+ 时间：2019.04.26
+ 场景：
  + 使用```Lists.transform```将```PO```转成```DTO```
  + 对转换后的```List```的```DTO```的```append```字段进行赋值
  + 返回的对象```List<DTO>```的```append```为null
  + 代码：
  	```
  	public class ListsTest {
  	
        @Test
        public void test() {
            PO po1 = new PO(1L);
            PO po2 = new PO(2L);
            List<PO> poList = Lists.newArrayList(po1, po2);
            List<DTO> dtoList = Lists.transform(poList, (po)->{
                DTO dto = new DTO();
                dto.setIdStr(po.getId().toString());
                return dto;
            });
            dtoList.forEach(dto -> {
                dto.setAppend(dto.getIdStr());
            });
            System.out.println(dtoList);
        }
    }

    @Data
    class PO {
        private Long id;

        public PO(Long id) {
            this.id = id;
        }
    }

    @Data
    class DTO {
        private String idStr;
        private String append;
    }
  	```

+ 原因：
  + ```Lists.transform```返回的List，真正的转换，是在使用其中元素的时候
  + 每次使用的时候，都是再次执行转换的```Function```
  + 因此每次都是新生成了一个对象，所以赋值无效

+ 解决方案：
  + 不使用Guava的Lists.transform

