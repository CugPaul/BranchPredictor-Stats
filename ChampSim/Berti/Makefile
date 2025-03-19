app = champsim

srcExt = cc
srcDir = src branch replacement prefetcher
objDir = obj
binDir = bin
inc = inc

debug = 1

CFlags = -Wall -g -std=c++17 -O3 -fno-stack-protector
LDFlags = -static
libs =
libDir =


#************************ DO NOT EDIT BELOW THIS LINE! ************************

ifeq ($(debug),1)
	debug=-g
else
	debug=
endif
inc := $(addprefix -I,$(inc))
libs := $(addprefix -l,$(libs))
libDir := $(addprefix -L,$(libDir))
CFlags += -c $(debug) $(inc) $(libDir) $(libs)
sources := $(shell find $(srcDir) -name '*.$(srcExt)') #找srcDir目录下.cc结尾的文件 返回名称
srcDirs := $(shell find . -name '*.$(srcExt)' -exec dirname {} \; | uniq) #返回含有.CC文件的目录路径
objects := $(patsubst %.$(srcExt),$(objDir)/%.o,$(sources)) #将.cc文件编译成.o文件放在obj下

ifeq ($(srcExt),cc)
	CC = $(CXX)
else
	CFlags += -std=gnu99
endif

.phony: all clean distclean


all: $(binDir)/$(app) #all一般是makefile的目标

#并不是顺序执行 而是依赖链执行，先看下面这句话 发现得先执行objects 
#最后一句是根据.o生成可执行文件
$(binDir)/$(app): buildrepo $(objects)
	@mkdir -p `dirname $@`
	@echo "Linking $@..."
	@$(CC) $(objects) $(LDFlags) -o $@

$(objDir)/%.o: %.$(srcExt) #这句话好像是.cc编译成.o文件
	@echo "Generating dependencies for $<..."
	@$(call make-depend,$<,$@,$(subst .o,.d,$@))
	@echo "Compiling $<..."
	@$(CC) $(CFlags) $< -o $@

clean:
	$(RM) -r $(objDir)

distclean: clean
	$(RM) -r $(binDir)/$(app)

#调用下面那个函数 创建文件夹
buildrepo:
	@$(call make-repo)

#在obj下为创建依次的目录
define make-repo 
   for dir in $(srcDirs); \
   do \
	mkdir -p $(objDir)/$$dir; \
   done
endef


# usage: $(call make-depend,source-file,object-file,depend-file)
define make-depend
  $(CC) -MM       \
        -MF $3    \
        -MP       \
        -MT $2    \
        $(CFlags) \
        $1
endef
