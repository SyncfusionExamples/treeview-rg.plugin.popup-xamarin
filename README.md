# How to show Xamarin.Forms TreeView (SfTreeView) in popup using Rg.Plugin.Popup framework ?

You can load the Xamarin.Forms [SfTreeView](https://help.syncfusion.com/xamarin/treeview/overview?) inside the popup using the [Rg.Plugin.Popup](https://github.com/rotorgames/Rg.Plugins.Popup) framework. Please follow the steps below to work with Rg plugin popup.

You can also refer the following article.

https://www.syncfusion.com/kb/11316/how-to-show-xamarin-forms-treeview-sftreeview-in-popup-using-rg-plugin-popup-framework


**Step 1:** Install the [Rg.Plugin.Popup](https://www.nuget.org/packages/Rg.Plugins.Popup) Nuget package in your shared code project.

**Step 2:** Initialize the popup plugin on each platform.

In Android, initialize the plugin on the MainActivity.cs page. Also, override the [OnButtonPressed](https://github.com/rotorgames/Rg.Plugins.Popup/wiki/Getting-started#android-back-button) method and invoke the SendBackPressed for back button to work with the plugin.

``` c#
namespace TreeViewXamarin.Droid
{
    [Activity(Label = "TreeViewXamarin", Icon = "@mipmap/icon", Theme = "@style/MainTheme", MainLauncher = true, ConfigurationChanges = ConfigChanges.ScreenSize | ConfigChanges.Orientation)]
    public class MainActivity : global::Xamarin.Forms.Platform.Android.FormsAppCompatActivity
    {
        protected override void OnCreate(Bundle savedInstanceState)
        {
            TabLayoutResource = Resource.Layout.Tabbar;
            ToolbarResource = Resource.Layout.Toolbar;
 
            base.OnCreate(savedInstanceState);
            Rg.Plugins.Popup.Popup.Init(this, savedInstanceState);
            global::Xamarin.Forms.Forms.Init(this, savedInstanceState);
            LoadApplication(new App());
        }
        
        public override void OnBackPressed()
        {
            if (Rg.Plugins.Popup.Popup.SendBackPressed(base.OnBackPressed))
            {
                // Do something if there are some pages in the `PopupStack`
            }
            else
            {
                // Do something if there are not any pages in the `PopupStack`
            }
        }
    }
}
```

In iOS, initialize the plugin on the AppDelegate.cs page.

``` c#
namespace TreeViewXamarin.iOS
{
    [Register("AppDelegate")]
    public partial class AppDelegate : global::Xamarin.Forms.Platform.iOS.FormsApplicationDelegate
    {
        public override bool FinishedLaunching(UIApplication app, NSDictionary options)
        {
            global::Xamarin.Forms.Forms.Init();
            SfTreeViewRenderer.Init();
            Rg.Plugins.Popup.Popup.Init();
            LoadApplication(new App());
 
            return base.FinishedLaunching(app, options);
        }
    }
}
```

In UWP, initialize the plugin on the App.xaml.cs page.

``` c#
protected override void OnLaunched(LaunchActivatedEventArgs e)
{
    ...
    if (rootFrame == null)
    {
        // Create a Frame to act as the navigation context and navigate to the first page
        rootFrame = new Frame();
 
        rootFrame.NavigationFailed += OnNavigationFailed;
 
        Rg.Plugins.Popup.Popup.Init();
        Xamarin.Forms.Forms.Init(e);
 
        if (e.PreviousExecutionState == ApplicationExecutionState.Terminated)
        {
            //TODO: Load state from previously suspended application
        }
        }
        ...
}
```

**Step 3:** Use the command to show the popup using [PopupNavigation](https://github.com/rotorgames/Rg.Plugins.Popup/wiki/Navigation) services in the ViewModel class and bind the command to the button.

``` xml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:TreeViewXamarin"
             x:Class="TreeViewXamarin.MainPage">
 
    <Button Text="Show Popup" 
            Command="{Binding ShowPopupCommand}" 
            HeightRequest="50" 
            WidthRequest="150"
            HorizontalOptions="Center" 
            VerticalOptions="Center"
            BindingContext="{local:FileManagerViewModel}"/>
</ContentPage>
```

Defining the command in the ViewModel.

``` c#
public Command ShowPopupCommand { get; set; }
 
public FileManagerViewModel()
{
    ShowPopupCommand = new Command(OnShowPopupClicked);
    GenerateSource();
}
private async void OnShowPopupClicked(object obj)
{
    await PopupNavigation.Instance.PushAsync(new TreeViewPopupPage());
}
```

**Step 4:** Create a new page as PopupPage and add SfTreeView to PopupPage.Content.

``` xml
<pages:PopupPage xmlns:pages="clr-namespace:Rg.Plugins.Popup.Pages;assembly=Rg.Plugins.Popup" 
                 xmlns="http://xamarin.com/schemas/2014/forms"
                 xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                 xmlns:local="clr-namespace:TreeViewXamarin"
                 xmlns:syncfusion="clr-namespace:Syncfusion.XForms.TreeView;assembly=Syncfusion.SfTreeView.XForms"
                 x:Class="TreeViewXamarin.TreeViewPopupPage">
 
    <pages:PopupPage.BindingContext>
        <local:FileManagerViewModel x:Name="viewModel"/>
    </pages:PopupPage.BindingContext>
 
    <pages:PopupPage.Content>
        <syncfusion:SfTreeView x:Name="treeView" ChildPropertyName="SubFiles" ItemTemplateContextType="Node" ItemsSource="{Binding ImageNodeInfo}" AutoExpandMode="AllNodesExpanded" VerticalOptions="Center" BackgroundColor="White" Margin="20,40" >
            <syncfusion:SfTreeView.ItemTemplate>
                <DataTemplate>
                    <Grid x:Name="grid" >
                        <Grid.ColumnDefinitions>
                            <ColumnDefinition Width="40" />
                            <ColumnDefinition Width="*" />
                        </Grid.ColumnDefinitions>
                        <Grid Padding="5,5,5,5">
                            <Image Source="{Binding Content.ImageIcon}" VerticalOptions="Center" HorizontalOptions="Center" HeightRequest="35" WidthRequest="35"/>
                        </Grid>
                        <Grid Grid.Column="1" RowSpacing="1" Padding="1,0,0,0" VerticalOptions="Center">
                            <Label LineBreakMode="NoWrap" Text="{Binding Content.ItemName}" VerticalTextAlignment="Center"/>
                        </Grid>
                    </Grid>
                </DataTemplate>
            </syncfusion:SfTreeView.ItemTemplate>
        </syncfusion:SfTreeView>
    </pages:PopupPage.Content>
</pages:PopupPage>
```

**Output**

![RgPluginPopupTreeView](https://github.com/SyncfusionExamples/treeview-rg.plugin.popup-xamarin/blob/master/ScreenShots/TreeViewPopup.jpg)
