## 1. RadDateTimePicker示例: ##
	<telerik:RadDateTimePicker  x:Name="radDateTimePicker" 
                                DateTimeWatermarkContent="请选择日期"
                                ErrorTooltipContent="请输入正确的日期格式!"
                                VerticalAlignment="Center" HorizontalAlignment="Center" Width="160"
                                DateSelectionMode="Month"
                                InputMode="DatePicker"
                                SelectedValue="3/31/2015"
                                SelectableDateStart="1/1/2010"
                                SelectableDateEnd="4/1/2015">
        <telerik:RadDateTimePicker.TooltipTemplate>
            <DataTemplate>
                <TextBlock Text="{Binding Path=TooltipContent, ElementName=radDateTimePicker}" FontWeight="Bold" Foreground="Red"/>
            </DataTemplate>
        </telerik:RadDateTimePicker.TooltipTemplate>
    </telerik:RadDateTimePicker>

    this.radDateTimePicker.Culture = new System.Globalization.CultureInfo("zh-cn")
    {
        DateTimeFormat =
        {
            ShortDatePattern = "yyyy年MM月"
        }
    };
> 此示例演示如何通过日期控件选择一定范围的年月,以及日期格式验证提示,格式化等.