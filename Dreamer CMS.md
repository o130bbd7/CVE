## Dreamer CMS v4.1.3 a stored xss vulnerability exists in "Column name(栏目名称)"
### Exploit Title
Dreamer CMS v4.1.3 a stored xss vulnerability exists in "Column name(栏目名称)"

### Google Dork
dreamer cms

### Date
2023/11/20

### Exploit Author
o130bbd7

### Vendor Homepage
```
http://cms.iteachyou.cc/
https://github.com/iteachyou-wjn/dreamer_cms
https://gitee.com/iteachyou/dreamer_cms
```
### Software Link
https://gitee.com/iteachyou/dreamer_cms/archive/refs/tags/Latest_Stable_Release_4.1.3.zip

### Version
4.1.3

### Tested on
windows11

### Vulnerability details
Storage xss vulnerability exists in "column name". Click Edit to change "Column Chinese name" to <img/src/onerror=prompt(1)> and save:
![image](https://github.com/o130bbd7/CVE/assets/87507397/8bcd3df3-b878-49bd-afe4-9609460587e1)
![image](https://github.com/o130bbd7/CVE/assets/87507397/dc22a4c5-cf04-4c90-bd55-7244d66e2c5c)

Return to the home page to trigger the xss vulnerability:
![image](https://github.com/o130bbd7/CVE/assets/87507397/6ef2f021-76ab-4994-8a64-6c430551f131)

### Vulnerability cause
Filter does not overwrite the getCnname method in the CategoryController.java file. The input is not filtered.
CategoryController.java:
```java
	@Log(operType = OperatorType.OTHER, module = "栏目管理", content = "修改栏目页面")
	@RequestMapping("/toEdit")
	@RequiresPermissions("system:category:toedit")
	public String toEdit(Model model,String id) {
		Category category = categoryService.selectById(id);
		if(category.getParentId().equals("-1")) {
			category.setParentName("顶级栏目");
		}else {
			Category category2 = categoryService.selectById(category.getParentId());
			category.setParentName(category2.getCnname());
		}
		
		List<Form> forms = formService.queryAll();
		System system = systemService.getSystem();
		model.addAttribute("category", category);
		model.addAttribute("forms", forms);
		model.addAttribute("system", system);
		return "admin/category/edit";
	}
```
Category.java:
```java
  @Column(name = "cnname")
  private String cnname;
```
