# FDMJ-tests

## 自动运行Test（推荐）

| hw | 运行方式 |
| --- | --- |
| hw06 | [README](./hw06/README.md) |
| hw05 | [README](./hw05/README.md) |
| hw05 | [README](./hw03_04/README.md) |

### hw1

* 将本仓库置于与FDMJ主仓库 `2024`平级的文件夹内
* 在FDMJ主仓库Makefile加入下面的命令
  ```makefile
  TEST_EXTERNAL_DIR=../../FDMJ-tests
  TEXT_EXTERNAL_HW=hw01
  clean_external: 
  	@$(RM) $(TEST_EXTERNAL_DIR)/bin
  	@find $(TEST_EXTERNAL_DIR) -type f \( \
  		-name "*.ll" -o -name "*.xml" -o -name "*.output" -o -name "gen_program_*" \
  		\) -exec $(RM) {} \;

  test_external: build clean_external
  	@mkdir -p $(TEST_EXTERNAL_DIR)/bin/; \
  	cp $(BUILD_DIR)/tools/main $(TEST_EXTERNAL_DIR)/bin/$(TEXT_EXTERNAL_HW); \
  	cp $(BUILD_DIR)/vendor/libsysy64.ll $(TEST_EXTERNAL_DIR)/bin/$(TEXT_EXTERNAL_HW)_libsysy64.ll; \
  	cd $(TEST_EXTERNAL_DIR)/$(TEXT_EXTERNAL_HW); \
  	python randomCodeGen.py; \
  	for file in $$(ls .); do \
  		if [ "$${file##*.}" = "fdmj" ]; then \
  			echo "$${file%%.*}"; \
  			../bin/$(TEXT_EXTERNAL_HW) "$${file%%.*}" < "$${file%%.*}".fdmj; \
  			$(LLVMLINK) --opaque-pointers "$${file%%.*}".ll ../bin/$(TEXT_EXTERNAL_HW)_libsysy64.ll -S -o "$${file%%.*}".ll.ll; \
  			$(LLI) -opaque-pointers "$${file%%.*}".ll.ll > "$${file%%.*}".output; echo $$?; \
  		fi \
  	done; \
  	python check.py;
  ```
* 运行 `make test_external`命令来重新构建最新的源代码，复制main文件到本仓库的bin/hwx/main，并运行对应的test
* 运行 `make clean_external`命令清除在测试仓库生成的文件

## 手动运行Test

* 将需要的 `.fdmj`和 `.correct`文件复制到对应的 `/test`文件夹下
* 将 `check.py`复制到 `/test`文件夹下
* 将 `randomCodeGen.py`复制到 `/test`文件夹下
* 运行 `make test`生成 `.output`
* 运行 `randomCodeGen.py` 随机生成 `.fdmj`和 `.correct`文件
* 运行 `python check.py`比较所有 `.output`和 `.correct`文件

## 贡献测试样例

* 先fork本仓库
* 做出修改（提交新的 `.fdmj` 和 `.correct`）文件
* 然后提出PR到Main分支
