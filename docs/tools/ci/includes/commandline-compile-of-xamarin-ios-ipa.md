
下面的命令行，以指定发布版本的解决方案**SOLUTION_FILE.sln**适用于 iPhone。 可以通过指定设置的位置的 IPA`IpaPackageDir`命令行上的属性：

 - 在 Mac 上，使用**xbuild**:

        xbuild /p:Configuration="Release" \ 
           /p:Platform="iPhone" \ 
           /p:IpaPackageDir="$HOME/Builds" \
           /t:Build MyProject.sln

**Xbuild**命令通常在目录中找到 **/Library/Frameworks/Mono.framework/Commands**。

 - 在 Windows 上，使用**msbuild**:

        msbuild /p:Configuration="Release" 
            /p:Platform="iPhone" 
            /p:IpaPackageDir="%USERPROFILE%\Builds" 
            /p:ServerAddress="192.168.1.3" /p:ServerUser="macuser"  
            /t:Build MyProject.sln


**msbuild**将不会自动展开`$( )`表达式中传递的命令行。 为此建议设置时使用的完整路径`IpaPackageDir`在命令行。


请参阅[发行说明适用于 iOS 9.8](https://developer.xamarin.com/releases/ios/xamarin.ios_9/xamarin.ios_9.8/#New_MSBuild_property_IpaPackageDir_to_customize_.ipa_output_location)有关更多细节`IpaPackageDir`属性。
