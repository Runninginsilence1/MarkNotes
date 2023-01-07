# ideavim的配置文件

```vimscript
nnoremap x "_x
nnoremap X "_X
nnoremap d "_d
nnoremap dd "_dd
nnoremap D "_D
vnoremap d "_d
vnoremap dd "_dd



nnoremap <leader>x ""x
nnoremap <leader>X ""X
nnoremap <leader>d ""d
nnoremap <leader>dd ""dd
nnoremap <leader>D ""D
vnoremap <leader>d ""d
vnoremap <leader>dd ""dd
""""""""""""""""""""""""""""""""""""""基础篇""""""""""""""""""""""""""""""""""""""
"debug神器
nmap <C-j> :action StepOver<CR>
nmap <C-k> :action Resume<CR>
nmap <C-h> :action StepOut<CR>
nmap <C-l> :action ForceStepInto<CR>
"移动专精
vmap <C-j> <C-e>
vmap <C-k> <C-y>
vmap <C-h> 10zh
vmap <C-l> 10zl
"移动专精
"imap <C-j> <Esc>ja
"imap <C-k> <Esc>ka
"imap <C-h> <Esc>10zhi
"imap <C-l> <Esc>l10zla
imap <C-j> <Down>
imap <C-k> <Up>
imap <C-h> <Left>
imap <C-l> <Right>
imap <C-i> <CR>
"左右视野
nnoremap zH 50zh
nnoremap zL 50zl
"^$符号太难按了
map gh ^
map gl $
"代码提示
"imap <C-n> <ESC>:action HippieCompletion<CR>a
"imap <C-p> <ESC>:action HippieBackwardCompletion<CR>a
""""""""""""""""""""""""""""""""""""""重构篇""""""""""""""""""""""""""""""""""""""
"元素名称重构
noremap <Space>re :action RenameElement<CR>
"移动重构
noremap <Space>mv :action Move<CR>
"成员变量转换为静态变量重构(配合移动重构有奇效)
noremap <Space>ms :action MakeStatic<CR>
"静态变量转换为成员变量重构
noremap <Space>ci :action ConvertToInstanceMethod<CR>
"内联重构(合并无效变量无效方法)
noremap <Space>il :action Inline<CR>
"抽取方法重构
noremap <Space>em :action ExtractMethod<CR>
"抽取接口重构
noremap <Space>ei :action ExtractInterface<CR>
"字段的访问限制
noremap <Space>ef :action EncapsulateFields<CR>
"切换方法为方法对象
noremap <Space>rmo :action ReplaceMethodWithMethodObject<CR>
"引入临时变量
noremap <Space>iv :action IntroduceVariable<CR>
"引入常量
noremap <Space>ic :action IntroduceConstant<CR>
"引入方法参数
noremap <Space>ip :action IntroduceParameter<CR>
"引入字段
noremap <Space>if :action IntroduceField<CR>
"引入参数对象(指定参数)
noremap <Space>po :action IntroduceParameterObject<CR>
"方法下放到子类
noremap <Space>pd :action MemberPushDown<CR>
"方法上移到父类
noremap <Space>pu :action MembersPullUp<CR>
"文件名称重构
noremap <Space>RF :action RenameFile<CR>
"修改方法签名
noremap <Space>cs  :action ChangeSignature<CR>
"抽取类
noremap <Space>ec :action ExtractClass<CR>
"匿名类到内部类
noremap <Space>ai :action AnonymousToInner<CR>
""""""""""""""""""""""""""""""""""""""跳转篇""""""""""""""""""""""""""""""""""""""
"搜索任何位置
noremap <Space>te :action SearchEverywhere<CR>
"特殊变量跳转
nnoremap <Space>ts mm`m:action GotoSymbol<CR>
"url地址跳转
noremap  <Space>tu  mm`m:action Toolkit.GotoService<CR>
"动作跳转
nnoremap <Space>ta mm`m:action GotoAction<CR>
"文件跳转
noremap <Space>tf mm`m:action GotoFile<CR>
"测试类跳转
noremap <Space>tt mm`m:action GotoTest<CR>
"java类跳转
noremap <Space>tc mm`m:action GotoClass<CR>
"文本跳转
noremap <Space>tp mm`m:action FindInPath<CR>
"跳转下个报错处
noremap <Space>ne mm`m:action GotoNextError<CR>
"跳转上个报错处
noremap <Space>pe mm`m:action GotoPreviousError<CR>
"从子类方法跳到父类的方法
noremap <Space>gs mm`m:action GotoSuperMethod<CR>
"跳到方法实现
noremap <Space>gi mm`m:action GotoImplementation<CR>
"跳转到声明
noremap <Space>gd mm`m:action GotoDeclaration<CR>
"跳转到下个改动处
noremap <Space>g, mm`m:action JumpToLastChange<CR>
"跳转到上个改动处
noremap <Space>g; mm`m:action JumpToNextChange<CR>
"最近查看文件间相互跳转
noremap <Space>rf mm`m:action RecentFiles<CR>
"最近改动文件间相互跳转
noremap <Space>rF mm`m:action RecentChangedFiles<CR>
"项目之间的跳转(上一个)
noremap <Space>nw :action NextProjectWindow<CR>
"项目之间的跳转(下一个)
noremap <Space>pw :action PreviousProjectWindow<CR>
"跳转 需要安装AceJump插件
noremap <Space>tj mm`m:action AceWordAction<CR>
noremap <Space>jp mm`m:action AceDeclarationAction<CR>
""""""""""""""""""""""""""""""""""""""SQL篇""""""""""""""""""""""""""""""""""""""
"sql提交
noremap <Space>sc :action Console.Transaction.Commit<CR>
"sql回滚
noremap <Space>sr :action Console.Transaction.Rollback<CR>
"sql执行
noremap <Space>se :action Console.Jdbc.Execute<CR>
"sql执行计划
noremap <Space>ep :action Console.Jdbc.ExplainPlan<CR>
"sql-raw执行计划
noremap <Space>EP :action Console.Jdbc.ExplainPlan.Raw<CR>

"打开数据库日志窗口,前提安装了MyBatis Log Plugin插件
noremap <Space>sl :action TailMyBatisLog0<CR>
noremap <Space>SL :action ActivateMyBatisLogToolWindow<CR>
noremap <Space>AL :action TailMyBatisLog0<CR>:action ActivateMyBatisLogToolWindow<CR>
""""""""""""""""""""""""""""""""""""""运行篇""""""""""""""""""""""""""""""""""""""
"设置运行配置
nnoremap <Space>rc :action RunConfiguration<CR>
"运行上次运行的类
noremap <Space>rr :action Run<CR>
"运行当前(now)类
noremap <Space>rn :action RunClass<CR>
"debug上次运行的类
noremap <Space>dd :action Debug<CR>
"debug当前(now)类
noremap <Space>dn :action DebugClass<CR>
"统计单元测试覆盖率运行上个类
noremap <Space>cc :action Coverage<CR>
"统计单元测试覆盖率运行当前(now)类
noremap <Space>cn :action RunCoverage<CR>
"项目停止运行
noremap <Space>sp   :action Stop<CR>
""""""""""""""""""""""""""""""""""""""Debug篇""""""""""""""""""""""""""""""""""""""
"开启或关闭当前行断点
noremap <Space>bp :action ToggleLineBreakpoint<CR>
"debug时快速计算表达式
noremap <Space>qe :action QuickEvaluateExpression <CR>
"debug计算器
noremap <Space>ee :action EvaluateExpression <CR>
"debug强制返回
noremap <Space>fr :action Debugger.ForceEarlyReturn<CR>
"添加新的debug观察
noremap <Space>daw :action Debugger.AddToWatch<CR>
"debug强制运行到光标处
noremap <Space>fc :action ForceRunToCursor<CR>
"安装了JRebel且在JRebel debug下才生效,热更新
noremap <Space>hs :action Hotswap<CR>
"安装了JRebel的debug运行
noremap <Space>DD :action JRebel Debug<CR>
"去除所有断点在当前文件中
noremap <Space>rab :action Debugger.RemoveAllBreakpointsInFile<CR>
"去除所有断点
noremap <Space>raB :action Debugger.RemoveAllBreakpoints<CR>
"弹出帧
noremap <Space>df :action Debugger.PopFrame<CR>
"显示当前断点位置
noremap <Space>pp :action ShowExecutionPoint<CR>
"流追踪技术
noremap <Space>ds :action StreamTracerAction<CR>
"哑断点技术
noremap <Space>mb :action XDebugger.MuteBreakpoints<CR>
"条件断点编辑
noremap <Space>eb :action EditBreakpoint<CR>

""""""""""""""""""""""""""""""""""""""窗口篇""""""""""""""""""""""""""""""""""""""
"激活maven窗口
noremap <Space>AM  :action ActivateMavenToolWindow<CR>
"激活database窗口
noremap <Space>AD  :action ActivateDatabaseToolWindow<CR>
"激活todo的窗口
noremap <Space>AT   :action ActivateTODOToolWindow<CR>
"激活Favorites的窗口
noremap <Space>AF :action ActivateFavoritesToolWindow<CR>
"激活project的窗口
noremap <Space>AP :action ActivateProjectToolWindow<CR>
"激活git的窗口
noremap <Space>AG :action ActivateVersionControlToolWindow<CR>
"激活git的窗口
noremap <Space>AS :action ActivateServicesToolWindow<CR>
"激活Breakpoints的窗口.
noremap <Space>AB :action ViewBreakpoints<CR>
"激活Rest的窗口(需要安装插件)
noremap <Space>AR :action ActivateRestServicesToolWindow<CR>
"激活debug的运行窗口
nnoremap <Space>wd :action ActivateDebugToolWindow<CR>
"激活run的运行窗口
nnoremap <Space>wr :action ActivateRunToolWindow<CR>
"隐藏窗口(windows hide)
nnoremap <Space>wh :action HideActiveWindow<CR>
"窗口最大化windows max
nnoremap <Space>mm :action MaximizeToolWindow<CR>
"隐藏所有窗口
noremap <Space>ha :action HideAllWindows<CR>
"noremap <Space><Space> :action HideAllWindows<CR>
"激活窗口,选中当前位置
noremap <Space>si :action SelectInProjectView<CR>

""""""""""""""""""""""""""""""""""""""信息篇""""""""""""""""""""""""""""""""""""""
"弹出警告信息
noremap <Space>ed :action ShowErrorDescription<CR>
"弹出类型信息
noremap <Space>ti :action ExpressionTypeInfo<CR>
"弹出参数信息
noremap <Space>pi :action ParameterInfo<CR>
"快速显示当前方法信息
noremap <Space>jd :action QuickJavaDoc<CR>
"弹出文件结构信息
noremap <Space>fs mm`m:action FileStructurePopup<CR>
"显示maven的依赖层级信息
noremap <Space>sd  :action ShowUmlDiagram<CR>
"弹出方法调用层级信息
noremap <Space>ch :action CallHierarchy<CR>
"查找引用了当前方法的文件信息
nnoremap <Space>fu :action FindUsages<CR>
"查找方法内使用该变量的引用信息
nnoremap <Space>su :action ShowUsages<CR>
"弹出方法结构信息
noremap <Space>mh :action MethodHierarchy<CR>
"弹出设置信息
noremap <Space>ss  :action ShowSettings<CR>
"弹出项目结构信息
noremap <Space>ps :action ShowProjectStructureSettings<CR>
""""""""""""""""""""""""""""""""""""""项目篇""""""""""""""""""""""""""""""""""""""
"关闭项目
noremap <Space>cP :action CloseProject<CR>
noremap <Space>CP :action CloseProject<CR>
"打开项目
noremap <Space>oP :action OpenFile<CR>
noremap <Space>OP :action OpenFile<CR>
"新建项目
noremap <Space>nP :action NewProject<CR>
noremap <Space>NP :action NewProject<CR>
""""""""""""""""""""""""""""""""""""""实用工具篇""""""""""""""""""""""""""""""""
"代码环绕
noremap <Space>sw :action SurroundWith<CR>
"整理代码
noremap <Space>= :action ReformatCode<CR>
"弹出菜单
noremap <Space>sm :action ShowPopupMenu<CR>
"保存模板
noremap <Space>st :action SaveAsTemplate<CR>
"复制当前文件的绝对路径
noremap <Space>cap   :action CopyAbsolutePath<CR>
"复制当前文件名
noremap <Space>ccp   :action CopyContentRootPath<CR>
"优化导入
noremap <Space>oi :action OptimizeImports<CR>
"改变视图
nnoremap <Space>cv :action ChangeView<CR>
"关闭到只剩当前tab栏
noremap <Space>co :action CloseAllEditorsButActive<CR>
"g4文件生成代码(需要安装anltr4插件)
noremap <Space>ag :action antlr.Generate<CR>:action antlr.Generate<CR>
"显示字节码
noremap <Space>bc   :action ByteCodeViewer<CR>
"快速翻译(需要安装翻译插件)
noremap <Space>qq   :action $TranslateTextComponent<CR>
"替换翻译(需要安装翻译插件)
noremap <Space>tr :action $TranslateAndReplaceAction<CR>
noremap <Space>gfu :action GenerateFullRestUrl<CR>
"添加到收藏文件
noremap <Space>atf :action AddToFavorites<CR>
"复制文件元素
noremap <Space>cE :action CopyElement<CR>
"新建文件元素
noremap <Space>nE :action NewElement<CR>
"删除当前文件元素
noremap <Space>dE :action SafeDelete<CR>
nnoremap <Space>cp :action CheckinProject<CR>
"idea智能提示
nnoremap <CR><CR> :action ShowIntentionActions<CR>
"跳转下个报错处(这个与idea智能提示配起来,炒鸡爽)
nnoremap <Space><CR> :action GotoNextError<CR>
"新建类,比nE快
noremap <Space>nc :action NewClass<CR>
"打开项目设置
noremap <Space>ps :action ShowProjectStructureSettings<CR>
"露出文件夹
noremap <Space>ri :action RevealIn<CR>
"代码生成
noremap <Space>ga :action Generate<CR>
"删除未修改的tab
noremap <Space>cu :action CloseAllUnmodifiedEditors<CR>
"Codota查找类似代码，需要提前安装Codota插件
noremap <Space>oc :action OpenCodotaSearch<CR>
"Codota激活窗口，需要提前安装Codota插件
noremap <Space>AC :action ActivateCodotaToolWindow<CR>
"Codota激活窗口，需要提前安装Codota插件
noremap <Space>cl :action CodotaLeanSearchCrossRefAction<CR>
"CSDN万能工具，需要提前安装CSDN插件
noremap <Space>csdn :action SearchByCSDN<CR>
"大小写，驼峰，下划线，中划线转换
noremap <Space>uu :action de.netnexus.CamelCasePlugin.ToggleCamelCase<CR>
"替换
noremap <Space>rp :action ReplaceInPath<CR>
"vim配置操作
noremap <Space>vm :action VimActions<CR>
"查看代码git作者
noremap <Space>us :action Annotate<CR>
"打开ddl语句
noremap <Space>dl :action DatabaseView.OpenDdlInConsole<CR>
"大小写切换，必须要先安装插件
noremap <Space>u :action de.netnexus.CamelCasePlugin.ToggleCamelCase<CR>
```
