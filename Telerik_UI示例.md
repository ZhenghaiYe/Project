## 1. RadDateTimePicker示例: ##
	private DateTime _selectableDateEnd;
	private DateTime _selectableDateStart;
	private DateTime? _selectedDate;
	
	public DateTime? SelectedDate
	{
	    get { return _selectedDate; }
	    set { SetValue("SelectedDate", value, ref _selectedDate); }
	}
	
	public DateTime SelectableDateStart
	{
	    get { return _selectableDateStart; }
	    set { SetValue("SelectableDateStart", value, ref _selectableDateStart); }
	}
	
	public DateTime SelectableDateEnd
	{
	    get { return _selectableDateEnd; }
	    set { SetValue("SelectableDateEnd", value, ref _selectableDateEnd); }
	}
	
	<telerik:RadDateTimePicker x:Name="RadDateForYearMonth"
	                           telerik:StyleManager.Theme="Windows7"
	                           DateTimeWatermarkContent="请选择日期"
	                           ErrorTooltipContent="请输入正确的日期格式!"
	                           VerticalAlignment="Center" HorizontalAlignment="Center" Width="100"
	                           DateSelectionMode="Month"
	                           InputMode="DatePicker"
	                           SelectedValue="{Binding SelectedDate,Mode=TwoWay,UpdateSourceTrigger=PropertyChanged}"
	                           SelectableDateStart="{Binding SelectableDateStart}"
	                           SelectableDateEnd="{Binding SelectableDateEnd}">
	    <telerik:RadDateTimePicker.TooltipTemplate>
	        <DataTemplate>
	            <TextBlock Text="{Binding Path=TooltipContent, ElementName=RadDateForYearMonth}"
	                       FontWeight="Bold" Foreground="Red" />
	        </DataTemplate>
	    </telerik:RadDateTimePicker.TooltipTemplate>
	</telerik:RadDateTimePicker>
	
	RadDateForYearMonth.Culture = new CultureInfo("zh-cn")
	{
	    DateTimeFormat =
	    {
	        ShortDatePattern = "yyyy年MM月"
	    }
	};

> 此示例演示如何通过日期控件选择一定范围的年月,以及日期格式验证提示,格式化等.