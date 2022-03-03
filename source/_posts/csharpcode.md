title: C#函数
category: 技能
date: 2019-08-14
index_img: 

---

C#函数

<!--more-->

<!--
 * @Author: 柯军
 * @Date: 2019-08-14 19:01:54
 * @Description: 
 -->
### C#函数

```
    public IList<CR_BondPoolCategory> GetBondPoolCategory()
        {
            return _svc.Context.CR_BondPoolCategory.ToList();
        }

        public bool SaveBondPoolCategory(List<CR_BondPoolCategory> res,string user)
        {
            var result = _svc.Context.CR_BondPoolCategory.ToList();
            // _svc.Context.CR_BondPoolCategory.RemoveRange(result);
            var _result = new List<CR_BondPoolCategory>();
            foreach (var s in res)
            {
                var pool = result.FirstOrDefault(t => t.BondPoolName == s.BondPoolName);
                if (pool == null)
                {
                    s.CreateTime = DateTime.Now;
                    s.CreateUser = user;
                    _result.Add(s);
                    _svc.Context.CR_BondPoolCategory.Add(s);
                }
            }

            _svc.Context.SaveChanges();
            return true;
        }
```


### Map映射
```

using System.Data.Entity.ModelConfiguration;

namespace Lib.Services.Models.Mapping
{
    public class CR_BondPoolCategoryMap : EntityTypeConfiguration<CR_BondPoolCategory>
    {
        public CR_BondPoolCategoryMap()
        {
            this.HasKey(t => t.ID);

            // Table & Column Mappings
            this.ToTable("CR_BondPoolCategory");
            this.Property(t => t.ID).HasColumnName("ID");
            this.Property(t => t.BondPoolName).HasColumnName("BondPoolName");
            this.Property(t => t.BondPoolType).HasColumnName("BondPoolType");
            this.Property(t => t.BondPoolValue).HasColumnName("BondPoolValue");
            this.Property(t => t.CreateUser).HasColumnName("CreateUser");
            this.Property(t => t.CreateTime).HasColumnName("CreateTime");
        }
    }
}
```

### HYcontext文件
```
    public DbSet<CR_BondPoolCategory> CR_BondPoolCategory { get; set; }

    protected override void OnModelCreating(DbModelBuilder modelBuilder)
    {
        modelBuilder.Configurations.Add(new CR_BondPoolCategoryMap());
    }
  ```
  
  ### Services -> Mounter 接口注册文件
  ```
        /// <summary>
        /// 行业利差分析接口
        /// </summary>
        public static ISpreadSvc Spread(this Svc svc)
        {
            return SvcFactory.CreateSvcInstance<SpreadSvc>(svc);
        }

        /// <summary>
        /// 流程管理接口
        /// </summary>
        public static IApprovalSvc Approval(this Svc svc)
        {
            return SvcFactory.CreateSvcInstance<ApprovalSvc>(svc);
        }
```