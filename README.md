# Cocos2d-Lua-Api-Geneartor
Follow these steps to generate the Cocos2d-Lua API files which can be used by BebeLua in VisualStudio.

## Environment
1. Windows 10
2. Visual Studio 2015
3. cocos2d-x-3.14.1

## What should be done
>1. Follow the instructions in cocos2d-x-3.14.1\tools\tolua\README.mdown to prepare your environment.
>   Following is what I do.
>   1) Make sure that you have installed `android-ndk-r10c` or later. I have got "android-ndk-r10e".
>   2) Download and install the latest 32 bit version python2.7.x from https://www.python.org. 
>      I use https://www.python.org/ftp/python/2.7.13/python-2.7.13.msi. Add the python to the environment variable "path".
>   3) Download and insatll pyyaml. I use python to do this. 
>      pip install pyyaml
>   4) Download and insatll Cheetah. I use python to do this.
>      pip install Cheetah
>   All these tools are needed by cocos2d to generate luabinding glue codes and the api doc files.
>   
>2. Modify the script files so that they can work properly on your environment. 
>   You can modify these files from the original cocos2d source codes. 
>   Your can also modify these files in the project which you created 
>   with commmand, cocos new ProjectName -p com.company.projectname -d ProjectDir -l lua.
>   1) Modify cocos2d-x-3.14.1\tools\tolua\genbindings.py according to your NDK. I have llvm-3.6 and llvm-3.5 in my NDK's toolchains folder.
>   After this step, I can successfully generate the luabinding codes and the lua official doc files.
>   ### Begin
>    if platform == 'win32':
>        x86_llvm_path = os.path.abspath(os.path.join(ndk_root, 'toolchains/llvm-3.6/prebuilt', '%s' % cur_platform))
>        if not os.path.exists(x86_llvm_path):
>            x86_llvm_path = os.path.abspath(os.path.join(ndk_root, 'toolchains/llvm-3.5/prebuilt', '%s' % cur_platform))
>    else:
>        x86_llvm_path = os.path.abspath(os.path.join(ndk_root, 'toolchains/llvm-3.6/prebuilt', '%s-%s' % (cur_platform, 'x86')))
>        if not os.path.exists(x86_llvm_path):
>            x86_llvm_path = os.path.abspath(os.path.join(ndk_root, 'toolchains/llvm-3.5/prebuilt', '%s-%s' % (cur_platform, 'x86')))
>
>    x64_llvm_path = os.path.abspath(os.path.join(ndk_root, 'toolchains/llvm-3.6/prebuilt', '%s-%s' % (cur_platform, 'x86_64')))
>    if not os.path.exists(x64_llvm_path):
>        x64_llvm_path = os.path.abspath(os.path.join(ndk_root, 'toolchains/llvm-3.5/prebuilt', '%s-%s' % (cur_platform, 'x86_64')))
>   ### End
>   
>   ### Begin
>   if '3.6' in llvm_path:
>        config.set('DEFAULT', 'clang_version', '3.6')
>    else:
>        config.set('DEFAULT', 'clang_version', '3.5')
>   ### End
>   
>   2) Modify cocos2d-x-3.14.1\tools\bindings-generator\targets\lua\templates\apidoc_function.script to generate function declarations. 
>      And empty cocos2d-x-3.14.1\tools\bindings-generator\targets\lua\templates\apidoc_function_foot.script with a blank left.
>    --------------------------------
>    --- summary
>    --- ${comment}
>    -- @function [parent=#${class_name}] ${func_name} 
>    -- @param self
>    #if $min_args > 0 
>        #set $count = 0
>        #for $arg in $arguments
>            #set $argValue = "arg" + str(count)
>            #if $arg.is_enum
>    -- @param #int $argumtntTips[$count]
>            #else
>    -- @param #$generator.lua_typename_from_natve($arg.namespaced_name) $argumtntTips[$count]
>            #end if
>            #set $count = $count + 1
>        #end for
>    #end if
>    #if str($ret_type) != "void"
>        #if $ret_type.is_enum
>    -- @return int#int ret (return value: int)
>        #else
>    -- @return $generator.lua_typename_from_natve($ret_type.namespaced_name, True)#$generator.lua_typename_from_natve($ret_type.namespaced_name, True) ret (return value: $generator.lua_typename_from_natve($ret_type.namespaced_name))
>        #end if
>    #else
>    -- @return $generator.lua_typename_from_natve($namespaced_class_name, True)#$generator.lua_typename_from_natve($namespaced_class_name, True) self (return value: $generator.lua_typename_from_natve($namespaced_class_name))
>    #end if
>    #set $argTips_info = ""
>    #set $argTips_index = 0
>    #set $argTips_count = len($argumtntTips)
>    #for $argTip in $argumtntTips
>        #if $argTips_index < $argTips_count - 1
>            #set $argTips_info = $argTips_info + $argumtntTips[$argTips_index] + ","
>        #else
>            #set $argTips_info = $argTips_info + $argumtntTips[$argTips_index]
>        #end if
>        #set $argTips_index = $argTips_index + 1
>    #end for
>    function ${generator.target_ns}.${class_name}.${func_name}($argTips_info)
>    end
>
>3. Use cocos2d-x-3.14.1\tools\tolua\genbindings.py to generate the luabing codes and lua api doc files.
>   1) Create a new project or you may already have one. Modify the files as preceding steps.
>   2) Run python YourLuaProject\frameworks\cocos2d-x\tools\tolua\genbindings.py.
>   3) The generated files will be under YourLuaProject\frameworks\cocos2d-x\cocos\scripting\lua-bindings\auto.
>   The original generated function doc looks like this. https://stevedonovan.github.io/ldoc/manual/doc.md.html
>   --------------------------------
>   -- Initializes a sprite with an SpriteFrame. The texture and rect in SpriteFrame will be applied on this sprite.<br>
>   -- param   spriteFrame  A SpriteFrame object. It should includes a valid texture and a rect.<br>
>   -- return  True if the sprite is initialized properly, false otherwise.
>   -- @function [parent=#Sprite] initWithSpriteFrame 
>   -- @param self
>   -- @param #cc.SpriteFrame spriteFrame
>   -- @return bool#bool ret (return value: bool)
>   Now a function declaration has been added.
>   --------------------------------
>   --- summary
>   --- Initializes a sprite with an SpriteFrame. The texture and rect in SpriteFrame will be applied on this sprite.<br>
>   -- param   spriteFrame  A SpriteFrame object. It should includes a valid texture and a rect.<br>
>   -- return  True if the sprite is initialized properly, false otherwise.
>   -- @function [parent=#Sprite] initWithSpriteFrame 
>   -- @param self
>   -- @param #cc.SpriteFrame spriteFrame
>   -- @return bool#bool ret (return value: bool)
>   function cc.Sprite.initWithSpriteFrame(spriteFrame)
>   end
>  
>  4. Copy YourLuaProject\frameworks\cocos2d-x\cocos\scripting\lua-bindings\auto\api folder into Documents\BabeLua\Completion\Cocos2d-Lua-V3.14.1.
>     And then the BaeLua can use these doc file to provide the code auto completion function in your Visual Studio.
>      
