# jmockit 使用相关问题

1. 使用 `mockUp`  mock 相关类的方法时，方法签名一定要一致 包括 返回值、方法名、参数。都需要一致，返回值可以是被 mock 方法的父类，但是参数不能是被 mock 方法的子类：

   ```java
   
   /**
   * 接口
   */
   public interface Repository {
       Object selectById(Serializable id);
   }
   public class RepositoryImpl implements Repository {
       @Override
       public Object selectById(Serializable id) {
           // ......
       }
   }
   
   public void TestClass {
       @Autowired
       private Repository repository;
    	
       @Before
       public void setUp() {
           new RepositoryMock();
           
       }
       @Test
       public void test() {
           Object o = repository.selectById("");
           Assert.assertNotNull(o);
           
       }
       // mock 类也是该接口的实现类
       public static class RepositoryMock extends MockUp<RepositoryImpl> {
           // 参数要严格匹配,如果将 Serializable 改成 String 等子类，则断言失败
           @Mock
           public Object selectById(Serializable id) {
               return new Object();
           }
       }
       
   }
   ```

   