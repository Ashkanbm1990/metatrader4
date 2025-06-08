//+------------------------------------------------------------------+
//|                                      BoxDrawerExtended_Fibo.mq4  |
//|                                    Integrated Box & Fibonacci    |
//+------------------------------------------------------------------+
#property copyright "User"
#property version   "4.10"
#property strict
#property indicator_chart_window
#property indicator_buffers 0

// لیست تمپلیت‌های از پیش تعریف شده
enum ENUM_TEMPLATE_NAMES
{
    TEMPLATE_TRADE = 0,        // trade
    TEMPLATE_DEFAULT = 1,      // default
    TEMPLATE_DAILY = 2,        // daily
    TEMPLATE_HOURLY = 3,       // hourly
    TEMPLATE_M15 = 4,          // m15
    TEMPLATE_DAILY_BACKTEST = 5,   // daily_backtest
    TEMPLATE_HOURLY_BACKTEST = 6,  // hourly_backtest
    TEMPLATE_M15_BACKTEST = 7      // m15_backtest
};
// پارامترهای ورودی
input ENUM_TEMPLATE_NAMES SelectedTemplate = TEMPLATE_TRADE; // انتخاب تمپلیت
input int SaveIntervalSeconds = 5; // فاصله بررسی تغییرات (ثانیه)
input bool EnableVerboseLogging = false; // نمایش جزئیات لاگ

//--- تنظیمات باکس
input color           InpBoxColor           = LightGray;
input int             InpLineWidth          = 2;
input ENUM_LINE_STYLE InpBoxStyle           = STYLE_SOLID;
input color           InpHighLineColor      = clrRed;
input color           InpLowLineColor       = clrBlue;
input color           InpOpenLineColor      = clrGreen;
input color           InpCloseLineColor     = clrMagenta;
input string          InpBoxDrawKey         = "A";        // کلید رسم باکس دستی
input string          InpExtendKey          = "E";        // کلید اکستند
input string          InpVerticalLineKey    = "V";        // کلید خط عمودی کامل
input color           InpVerticalLineColor  = clrYellow;  // رنگ خط عمودی
input string          InpHorizontalLineKey  = "H";        // کلید خط افقی کامل
input color           InpHorizontalLineColor= clrOrange;  // رنگ خط افقی

//--- تنظیمات فیبوناچی پیوت
input string Separator1         = "========== PIVOT FIBONACCI ==========";
input bool   PivotEnable        = true;
input string PivotHotkey        = "1";
input color  PivotColor         = clrBlue;
input ENUM_LINE_STYLE PivotStyle= STYLE_SOLID;
input int    PivotWidth         = 1;

input bool   PivotLevel_0_Enable    = true;
input color  PivotLevelColor        = clrBlue;
input double PivotLevel_0_Value     = 0.0;
input bool   PivotLevel_33_Enable   = true;
input double PivotLevel_33_Value    = 33.33333333;
input color  PivotLevel_33_Color    = clrGreen;
input bool   PivotLevel_50_Enable   = false;
input double PivotLevel_50_Value    = 50.0;
input bool   PivotLevel_66_Enable   = false;
input double PivotLevel_66_Value    = 66.666666;
input bool   PivotLevel_100_Enable  = true;
input double PivotLevel_100_Value   = 100.0;
input bool   PivotLevel_122_Enable  = false;
input double PivotLevel_122_Value   = -22.2223;
input bool   PivotLevel_133_Enable  = false;
input double PivotLevel_133_Value   = -33.33333;
input bool   PivotLevel_155_Enable  = false;
input double PivotLevel_155_Value   = -55.55555;
input bool   PivotLevel_166_Enable  = false;
input double PivotLevel_166_Value   = -66.66666;
input bool   PivotLevel_188_Enable  = false;
input double PivotLevel_188_Value   = -88.8888;
input color  PivotLevel_188_Color   = clrViolet;
input bool   PivotLevel_199_Enable  = false;
input double PivotLevel_199_Value   = 99.99;
input bool   PivotLevel_4_Enable    = false;
input double PivotLevel_4_Value     = -119.0;
input bool   PivotLevel_130_Enable  = false;
input double PivotLevel_130_Value   = -1.30;
input bool   PivotLevel_4236_Enable = false;
input double PivotLevel_4236_Value  = 423.6;
input bool   PivotLevel_Custom_Enable = false;
input double PivotLevel_Custom_Value  = 85.4;

//--- تنظیمات فیبوناچی اسپایک
input string Separator2          = "========== SPIKE FIBONACCI ==========";
input bool   SpikeEnable         = true;
input string SpikeHotkey         = "2";
input color  SpikeColor          = clrRed;
input ENUM_LINE_STYLE SpikeStyle = STYLE_SOLID;
input int    SpikeWidth          = 1;

input bool   SpikeLevel_0_Enable    = true;
input double SpikeLevel_0_Value     = 0.0;
input color  SpikeLevelColor        = clrBlack;
input bool   SpikeLevel_33_Enable   = true;
input double SpikeLevel_33_Value    = 33.3333333;
input color  SpikeLevel_33_Color    = clrBlack;
input bool   SpikeLevel_50_Enable   = true;
input double SpikeLevel_50_Value    = 50.0;
input bool   SpikeLevel_66_Enable   = false;
input double SpikeLevel_66_Value    = 66.666666;
input bool   SpikeLevel_100_Enable  = true;
input double SpikeLevel_100_Value   = 100.0;
input bool   SpikeLevel_122_Enable  = true;
input double SpikeLevel_122_Value   = -22.222222;
input color  SpikeLevel_Steps_Color = clrBlack;
input bool   SpikeLevel_133_Enable  = true;
input double SpikeLevel_133_Value   = -33.333333;
input bool   SpikeLevel_155_Enable  = true;
input double SpikeLevel_155_Value   = -55.5555555;
input bool   SpikeLevel_166_Enable  = true;
input double SpikeLevel_166_Value   = -66.6666666;
input bool   SpikeLevel_188_Enable  = FALSE;
input double SpikeLevel_188_Value   = -88.8888888;
input bool   SpikeLevel_199_Enable  = FALSE;
input double SpikeLevel_199_Value   = -99.99999;
input bool   SpikeLevel_4_Enable    = FALSE;
input double SpikeLevel_4_Value     = -120;
input bool   SpikeLevel_130_Enable  = FALSE;
input double SpikeLevel_130_Value   = -133.0;
input bool   SpikeLevel_4236_Enable = false;
input double SpikeLevel_4236_Value  = 423.6;
input bool   SpikeLevel_Custom_Enable = false;
input double SpikeLevel_Custom_Value  = 85.4;

//--- نام‌ها و متغیرهای عمومی باکس
string g_confirmName   = "BoxDrawerExtended_Box";
string g_highLineName  = "BoxDrawerExtended_High";
string g_lowLineName   = "BoxDrawerExtended_Low";
string g_openLineName  = "BoxDrawerExtended_Open";
string g_closeLineName = "BoxDrawerExtended_Close";
string boxPrefix       = "Boxc_";
string boxUserPrefix   = "Boxu_";
string extSuffix       = "_EXT";

int    g_selectedBar    = -1;
ulong  g_lastClickTick  = 0;
string g_lastBoxName    = "";
string g_lastExtName    = "";
int    g_objectsCount   = 0;
#define DOUBLE_CLICK_TICKS 300
#define SNAP_MAX_RATIO     0.9

bool     g_drawBoxMode   = false;
datetime g_boxStartTime  = 0;
double   g_boxStartPrice = 0;
bool     g_boxFirstClick = false;
bool     g_extendActive  = true;
int      g_lastMouseX    = 0;
int      g_lastMouseY    = 0;
// متغیرهای سراسری تمپلیت
string TemplateNames[8] = {"trade", "default", "daily", "hourly", "m15", "daily_backtest", "hourly_backtest", "m15_backtest"};
string CurrentTemplateName = "";
string LastObjectsHash = "";
int LastObjectsCount = 0;
datetime LastSaveTime = 0;
datetime LastCheckTime = 0;
bool IsShuttingDown = false;
bool HasUnsavedChanges = false;
//--- نام‌ها و متغیرهای عمومی فیبوناچی
string PivotObjectPrefix;
string SpikeObjectPrefix;
datetime PivotTime1, PivotTime2;
double   PivotPrice1, PivotPrice2;
datetime SpikeTime1, SpikeTime2;
double   SpikePrice1, SpikePrice2;

//+------------------------------------------------------------------+
//| وارد کردن توابع API ویندوز                                       |
//+------------------------------------------------------------------+
#import "user32.dll"
int   GetKeyState(int nVirtKey);
short GetAsyncKeyState(int vKey);
#import
#define VK_SHIFT 0x10
//+------------------------------------------------------------------+
//| تبدیل حرف به کد کلید                                            |
//+------------------------------------------------------------------+
int CharToKeyCode(string letter)
  {
   if(StringLen(letter) == 0) return 0;
   string firstChar = StringSubstr(letter, 0, 1);
   int charCode = (int)StringGetCharacter(firstChar, 0);
   if(charCode >= 97 && charCode <= 122) charCode -= 32;
   if(charCode >= 65 && charCode <= 90) return charCode;
   return 0;
  }

//+------------------------------------------------------------------+
//| دریافت کد کلید از رشته                                          |
//+------------------------------------------------------------------+
int GetKeyCode(string key)
  {
    if(key == "1") return 49;
    if(key == "2") return 50;
    if(key == "3") return 51;
    if(key == "4") return 52;
    if(key == "5") return 53;
    if(key == "6") return 54;
    if(key == "7") return 55;
    if(key == "8") return 56;
    if(key == "9") return 57;
    if(key == "0") return 48;
    if(key == "A" || key == "a") return 65;
    if(key == "B" || key == "b") return 66;
    if(key == "C" || key == "c") return 67;
    if(key == "D" || key == "d") return 68;
    if(key == "E" || key == "e") return 69;
    if(key == "F" || key == "f") return 70;
    if(key == "G" || key == "g") return 71;
    if(key == "H" || key == "h") return 72;
    if(key == "I" || key == "i") return 73;
    if(key == "J" || key == "j") return 74;
    if(key == "K" || key == "k") return 75;
    if(key == "L" || key == "l") return 76;
    if(key == "M" || key == "m") return 77;
    if(key == "N" || key == "n") return 78;
    if(key == "O" || key == "o") return 79;
    if(key == "P" || key == "p") return 80;
    if(key == "Q" || key == "q") return 81;
    if(key == "R" || key == "r") return 82;
    if(key == "S" || key == "s") return 83;
    if(key == "T" || key == "t") return 84;
    if(key == "U" || key == "u") return 85;
    if(key == "V" || key == "v") return 86;
    if(key == "W" || key == "w") return 87;
    if(key == "X" || key == "x") return 88;
    if(key == "Y" || key == "y") return 89;
    if(key == "Z" || key == "z") return 90;
    return 0;
  }

//+------------------------------------------------------------------+
//| بررسی فشرده شدن کلید فیبوناچی                                    |
//+------------------------------------------------------------------+
bool IsKeyPressed(string key)
  {
    static datetime lastPivotPress = 0;
    static datetime lastSpikePress = 0;
    if(!ChartGetInteger(ChartID(), CHART_BRING_TO_TOP)) return false;
    int keyCode = GetKeyCode(key);
    if(keyCode == 0) return false;
    bool currentState = (GetAsyncKeyState(keyCode) & 0x8000) != 0;
    if(key == PivotHotkey && currentState)
      {
        if(TimeCurrent() > lastPivotPress + 1)
          {
            lastPivotPress = TimeCurrent();
            return true;
          }
      }
    else if(key == SpikeHotkey && currentState)
      {
        if(TimeCurrent() > lastSpikePress + 1)
          {
            lastSpikePress = TimeCurrent();
            return true;
          }
      }
    return false;
  }

//+------------------------------------------------------------------+
//| تابع Init اندیکاتور ترکیبی                                       |
//+------------------------------------------------------------------+
int OnInit()
  {
   ChartRedraw();
   g_objectsCount = ObjectsTotal();
   EventSetTimer(1);
   
   // ایجاد نام منحصر به فرد برای هر چارت فیبوناچی
   long chartId = ChartID();
   PivotObjectPrefix = "Pivot_Fibo_" + IntegerToString(chartId) + "_";
   SpikeObjectPrefix = "Spike_Fibo_" + IntegerToString(chartId) + "_";
   // بازسازی اشیاء فیبوناچی در صورت نیاز
   RecreateAllObjects();
   
    // تنظیم نام تمپلیت انتخاب شده
    CurrentTemplateName = TemplateNames[SelectedTemplate];
    
    // تنظیم Timer با فاصله زمانی مشخص شده
    EventSetTimer(MathMax(SaveIntervalSeconds, 1));
    
    // اولیه‌سازی متغیرها
    LastObjectsCount = ObjectsTotal();
    LastObjectsHash = GenerateObjectsHash();
    LastCheckTime = TimeCurrent();
    
    // پیام شروع
    Print("Auto Template Saver فعال شد:");
    Print("- تمپلیت: ", CurrentTemplateName);
    Print("- فاصله بررسی: ", SaveIntervalSeconds, " ثانیه");
    Print("- چارت: ", Symbol());
   return(INIT_SUCCEEDED);
  }

//+------------------------------------------------------------------+
//| تابع Deinit اندیکاتور ترکیبی                                     |
//+------------------------------------------------------------------+
void OnDeinit(const int reason)
  {
  IsShuttingDown = true;
    
    // ذخیره نهایی اگر تغییری وجود دارد
    if(HasUnsavedChanges || reason == REASON_CHARTCLOSE || reason == REASON_PROGRAM)
    {
        SaveCurrentTemplate();
    }
    
    EventKillTimer();
    
    // پیام پایان
    string reasonText = "";
    switch(reason)
    {
        case REASON_REMOVE: reasonText = "حذف اندیکاتور"; break;
        case REASON_CHARTCLOSE: reasonText = "بسته شدن چارت"; break;
        case REASON_PROGRAM: reasonText = "بسته شدن MT4"; break;
        case REASON_PARAMETERS: reasonText = "تغییر پارامترها"; break;
        case REASON_CHARTCHANGE: reasonText = "تغییر چارت"; break;
        default: reasonText = "دلیل نامشخص"; break;
    }
    
    Print("Auto Template Saver خاموش شد (", reasonText, ") - ذخیره نهایی انجام شد");

 
   // حذف دکمه‌های باکس
   ObjectDelete(0, g_confirmName);
   ObjectDelete(0, g_highLineName);
   ObjectDelete(0, g_lowLineName);
   ObjectDelete(0, g_openLineName);
   ObjectDelete(0, g_closeLineName);
   // حذف اشیاء فیبوناچی در صورت حذف اندیکاتور (غیر از تغییر تایم‌فریم)
   if(reason != REASON_CHARTCHANGE)
     {
      DeletePivotFibonacci();
      DeleteSpikeFibonacci();
      ObjectDelete(0, "FiboInfo");
      ObjectDelete(0, "PivotInfo_" + IntegerToString(ChartID()));
      ObjectDelete(0, "SpikeInfo_" + IntegerToString(ChartID()));
     }
   Comment("");
  
  }

//+------------------------------------------------------------------+
//| تابع Calculate اندیکاتور ترکیبی                                  |
//+------------------------------------------------------------------+
int OnCalculate( const int rates_total,
   const int prev_calculated,
   const datetime &time[],     // ← آرایه کاملِ زمان
   const double   &open[],     // ← آرایه قیمت بازشدن
   const double   &high[],     // ← آرایه قیمت بیشینه
   const double   &low[],      // ← آرایه قیمت کمینه
   const double   &close[],    // ← آرایه قیمت بسته شدن
   const long     &tick_volume[],
   const long     &volume[],
   const int      &spread[])
  {
   // به‌روز کردن باکس‌ها
   CheckDeletedObjects();
   UpdateAllExtendedObjects();
   return(rates_total);
  }

//+------------------------------------------------------------------+
//| تابع OnTick                                                     |
//+------------------------------------------------------------------+
void OnTick()
  {
   CheckDeletedObjects();
   UpdateAllExtendedObjects();
  }

//+------------------------------------------------------------------+
//| تابع OnTimer                                                    |
//+------------------------------------------------------------------+
void OnTimer()
  {
   // به‌روز کردن باکس‌ها
   CheckDeletedObjects();
   UpdateAllExtendedObjects();
   // بررسی کلیدهای فیبوناچی و به‌روز کردن اطلاعات
   CheckHotkeys();
   UpdateFibonacciInfo();
   if(IsShuttingDown) return;
    
    // بررسی سبک و سریع تغییرات
    if(HasChartChanged())
    {
        HasUnsavedChanges = true;
        
        // ذخیره فقط اگر فاصله زمانی مناسب گذشته باشد
        datetime currentTime = TimeCurrent();
        if(currentTime - LastSaveTime >= SaveIntervalSeconds)
        {
            SaveCurrentTemplate();
            LastSaveTime = currentTime;
            HasUnsavedChanges = false;
        }
    }
    
    LastCheckTime = TimeCurrent();
  }

//+------------------------------------------------------------------+
//| تابع OnChartEvent ترکیبی                                         |
//+------------------------------------------------------------------+
void OnChartEvent(const int id,
                  const long &lparam,
                  const double &dparam,
                  const string &sparam)
  {
   CheckDeletedObjects();
   // ذخیره موقعیت ماوس برای باکس
   if(id == CHARTEVENT_MOUSE_MOVE)
     {
      g_lastMouseX = (int)lparam;
      g_lastMouseY = (int)dparam;
     }
   // حالت رسم باکس دستی (A)
   if(id == CHARTEVENT_KEYDOWN && lparam == CharToKeyCode(InpBoxDrawKey))
     {
      g_drawBoxMode = true;
      g_boxFirstClick = false;
      Comment("مد رسم باکس فعال شد؛ نقطه اول را انتخاب کنید.");
      return;
     }
   // رسم باکس با دو کلیک
   if(g_drawBoxMode && id == CHARTEVENT_CLICK)
     {
      int x = (int)lparam, y = (int)dparam;
      datetime clkTime;
      double clkPrice;
      int subWnd;
      bool hit = ChartXYToTimePrice(0, x, y, subWnd, clkTime, clkPrice);
      if(hit)
        {
         if(!g_boxFirstClick)
           {
            g_boxStartTime = clkTime;
            g_boxStartPrice = clkPrice;
            g_boxFirstClick = true;
            Comment("نقطه دوم باکس را انتخاب کنید.");
           }
         else
           {
            string boxName = boxUserPrefix + "_" + TimeToString(g_boxStartTime, TIME_DATE | TIME_SECONDS);
            ObjectCreate(0, boxName, OBJ_RECTANGLE, 0, g_boxStartTime, g_boxStartPrice, clkTime, clkPrice);
            ObjectSetInteger(0, boxName, OBJPROP_COLOR, InpBoxColor);
            ObjectSetInteger(0, boxName, OBJPROP_WIDTH, 2);
            ObjectSetInteger(0, boxName, OBJPROP_STYLE, InpBoxStyle);
            ObjectSetInteger(0, boxName, OBJPROP_BACK, true);
            ObjectSetInteger(0, boxName, OBJPROP_SELECTABLE, true);
            ObjectSetInteger(0, boxName, OBJPROP_SELECTED, FALSE);
            g_lastBoxName = boxName;
            g_objectsCount = ObjectsTotal();
            SnapBoxEdges(boxName);
            UpdateExtendedBox(boxName);
            g_drawBoxMode = false;
            g_boxFirstClick = false;
            Comment("");
           }
        }
      return;
     }
   // دابل کلیک برای باز کردن دکمه‌های باکس کلاسیک
   if(id == CHARTEVENT_CLICK)
     {
      ulong nowTick = GetTickCount();
      bool isDouble = (nowTick - g_lastClickTick) < DOUBLE_CLICK_TICKS;
      g_lastClickTick = nowTick;
      int x = (int)lparam, y = (int)dparam;
      datetime clkTime;
      double clkPrice;
      int subWnd;
      bool hit = ChartXYToTimePrice(0, x, y, subWnd, clkTime, clkPrice);
      int bar = hit ? iBarShift(NULL, 0, clkTime, false) : -1;
      DeleteConfirmButtons();
      if(bar >= 0 && isDouble)
        {
         datetime bt = iTime(NULL, 0, bar);
         double bh = iHigh(NULL, 0, bar), bl = iLow(NULL, 0, bar);
         double tol = (bh - bl) * 0.2;
         if(clkPrice >= bl - tol && clkPrice <= bh + tol)
           {
            g_selectedBar = bar;
            int bx, by;
            if(ChartTimePriceToXY(0, 0, bt, bh, bx, by))
               CreateConfirmButtons(bx - 30, by - 20);
           }
        }
     }
   // کلیک روی دکمه‌های تایید باکس
   if(id == CHARTEVENT_OBJECT_CLICK)
     {
      if(sparam == g_confirmName && g_selectedBar >= 0)
        {
         CreateMainBox(g_selectedBar);
         DeleteConfirmButtons();
        }
      else if(sparam == g_highLineName && g_selectedBar >= 0)
        {
         CreateHorizontalLine(g_selectedBar, 0);
         DeleteConfirmButtons();
        }
      else if(sparam == g_lowLineName && g_selectedBar >= 0)
        {
         CreateHorizontalLine(g_selectedBar, 1);
         DeleteConfirmButtons();
        }
      else if(sparam == g_openLineName && g_selectedBar >= 0)
        {
         CreateHorizontalLine(g_selectedBar, 2);
         DeleteConfirmButtons();
        }
      else if(sparam == g_closeLineName && g_selectedBar >= 0)
        {
         CreateHorizontalLine(g_selectedBar, 3);
         DeleteConfirmButtons();
        }
     }
   // اکستند (E) برای باکس/خط یا تغییر حالت کلی (Shift+E)
   if(id == CHARTEVENT_KEYDOWN && lparam == CharToKeyCode(InpExtendKey))
     {
      if((GetKeyState(VK_SHIFT) & 0x8000) != 0)
        {
         g_extendActive = !g_extendActive;
         if(!g_extendActive)
           {
            for(int i = ObjectsTotal() - 1; i >= 0; i--)
              {
               string objName = ObjectName(0, i);
               if(StringFind(objName, extSuffix) > 0)
                 ObjectDelete(0, objName);
              }
            Comment("");
           }
         else
           {
            Comment("");
           }
         ChartRedraw();
        }
      else
        {
         for(int i = ObjectsTotal() - 1; i >= 0; i--)
           {
            string objName = ObjectName(0, i);
            if(((StringFind(objName, boxPrefix) == 0) || (StringFind(objName, boxUserPrefix) == 0) ||
                StringFind(objName, "HighLine_") == 0 || StringFind(objName, "LowLine_") == 0 ||
                StringFind(objName, "OpenLine_") == 0 || StringFind(objName, "CloseLine_") == 0)
               && StringFind(objName, extSuffix) < 0
               && ObjectGetInteger(0, objName, OBJPROP_SELECTED))
              {
               ToggleExtendedObject(objName);
               break;
              }
           }
        }
     }
   // خط عمودی سراسری (V)
   if(id == CHARTEVENT_KEYDOWN && lparam == CharToKeyCode(InpVerticalLineKey))
     {
      datetime clkTime;
      double clkPrice;
      int subWnd;
      if(ChartXYToTimePrice(0, g_lastMouseX, g_lastMouseY, subWnd, clkTime, clkPrice))
        {
         CreateFullVerticalLine(clkTime);
        }
      return;
     }
   // خط افقی سراسری (H)
   if(id == CHARTEVENT_KEYDOWN && lparam == CharToKeyCode(InpHorizontalLineKey))
     {
      datetime clkTime;
      double clkPrice;
      int subWnd;
      if(ChartXYToTimePrice(0, g_lastMouseX, g_lastMouseY, subWnd, clkTime, clkPrice))
        {
         CreateFullHorizontalLine(clkPrice);
        }
      return;
     }
   // جابجایی یا تغییر اندازه باکس/خط افقی باکس
   if(id == CHARTEVENT_OBJECT_DRAG)
     {
      if(((StringFind(sparam, boxPrefix) == 0) || (StringFind(sparam, boxUserPrefix) == 0)) && StringFind(sparam, extSuffix) < 0)
        SnapBoxEdges(sparam);
      if((StringFind(sparam, "HighLine_") == 0 || StringFind(sparam, "LowLine_") == 0 ||
          StringFind(sparam, "OpenLine_") == 0 || StringFind(sparam, "CloseLine_") == 0) && StringFind(sparam, extSuffix) < 0)
        {
         datetime time1 = (datetime)ObjectGetInteger(0, sparam, OBJPROP_TIME1);
         double price1 = ObjectGetDouble(0, sparam, OBJPROP_PRICE1);
         datetime time2 = (datetime)ObjectGetInteger(0, sparam, OBJPROP_TIME2);
         ObjectSetDouble(0, sparam, OBJPROP_PRICE2, price1);
        }
      string extName = sparam + extSuffix;
      if(ObjectFind(0, extName) >= 0)
        UpdateExtendedLine(sparam);
     }

   //--- بخش فیبوناچی: تشخیص و جابجایی اشیاء
   if(id == CHARTEVENT_OBJECT_DRAG)
     {
      if(StringFind(sparam, PivotObjectPrefix) >= 0)
        UpdatePivotFibonacci();
      else if(StringFind(sparam, SpikeObjectPrefix) >= 0)
        UpdateSpikeFibonacci();
     }
   // تشخیص تغییر تایم‌فریم و بازسازی اشیاء فیبوناچی
   static int lastPeriod = Period();
   if(Period() != lastPeriod)
     {
      lastPeriod = Period();
      RecreateAllObjects();
     }
       // رصد تغییرات مهم
    switch(id)
    {
        case CHARTEVENT_OBJECT_CREATE:
        case CHARTEVENT_OBJECT_DELETE:
        case CHARTEVENT_OBJECT_CHANGE:
            HasUnsavedChanges = true;
            if(EnableVerboseLogging)
                Print("رویداد شی: ", sparam);
            break;
            
        case CHARTEVENT_CHART_CHANGE:
            HasUnsavedChanges = true;
            if(EnableVerboseLogging)
                Print("تغییر چارت شناسایی شد");
            break;
    }
  }

//+------------------------------------------------------------------+
//| حذف آبجکت‌های اکستند در صورت حذف آبجکت اصلی                     |
//+------------------------------------------------------------------+
void CheckDeletedObjects()
  {
   int currentCount = ObjectsTotal();
   if(currentCount < g_objectsCount)
     {
      for(int i = ObjectsTotal() - 1; i >= 0; i--)
        {
         string objName = ObjectName(0, i);
         if(StringFind(objName, extSuffix) > 0 &&
            (StringFind(objName, boxPrefix) == 0 || StringFind(objName, boxUserPrefix) == 0 ||
             StringFind(objName, "HighLine_") == 0 || StringFind(objName, "CloseLine_") == 0 ||
             StringFind(objName, "OpenLine_") == 0 || StringFind(objName, "LowLine_") == 0))
           {
            string mainObjName = StringSubstr(objName, 0, StringLen(objName) - StringLen(extSuffix));
            if(ObjectFind(0, mainObjName) < 0)
              ObjectDelete(0, objName);
           }
        }
     }
   g_objectsCount = ObjectsTotal();
  }

//+------------------------------------------------------------------+
//| به‌روز کردن همه‌ی باکس‌ها و خطوط اکستند شده                     |
//+------------------------------------------------------------------+
void UpdateAllExtendedObjects()
  {
   for(int i = 0; i < ObjectsTotal(); i++)
     {
      string name = ObjectName(0, i);
      if(StringFind(name, extSuffix) >= 0) continue;
      if(StringFind(name, boxPrefix) == 0 || StringFind(name, boxUserPrefix) == 0)
        {
         string extName = name + extSuffix;
         if(ObjectFind(0, extName) >= 0) UpdateExtendedBox(name);
        }
      else if(StringFind(name, "HighLine_") == 0 || StringFind(name, "LowLine_") == 0 ||
              StringFind(name, "OpenLine_") == 0 || StringFind(name, "CloseLine_") == 0)
        {
         string extName = name + extSuffix;
         if(ObjectFind(0, extName) >= 0) UpdateExtendedLine(name);
        }
     }
  }

//+------------------------------------------------------------------+
//| چسبندگی لبه باکس به OHLC                                         |
//+------------------------------------------------------------------+
void SnapBoxEdges(const string boxName)
  {
   if(ObjectFind(0, boxName) < 0) return;
   datetime time1  = (datetime)ObjectGetInteger(0, boxName, OBJPROP_TIME1);
   double price1   = ObjectGetDouble(0, boxName, OBJPROP_PRICE1);
   datetime time2  = (datetime)ObjectGetInteger(0, boxName, OBJPROP_TIME2);
   double price2   = ObjectGetDouble(0, boxName, OBJPROP_PRICE2);
   int bar1 = iBarShift(NULL, 0, time1, false);
   int bar2 = iBarShift(NULL, 0, time2, false);
   if(bar1 < 0 || bar2 < 0) return;
   double candidates1[4], candidates2[4];
   candidates1[0] = iHigh(NULL, 0, bar1);
   candidates1[1] = iLow(NULL, 0, bar1);
   candidates1[2] = iOpen(NULL, 0, bar1);
   candidates1[3] = iClose(NULL, 0, bar1);
   candidates2[0] = iHigh(NULL, 0, bar2);
   candidates2[1] = iLow(NULL, 0, bar2);
   candidates2[2] = iOpen(NULL, 0, bar2);
   candidates2[3] = iClose(NULL, 0, bar2);
   double range1 = MathAbs(candidates1[0] - candidates1[1]);
   double range2 = MathAbs(candidates2[0] - candidates2[1]);
   static datetime last_time1 = 0, last_time2 = 0;
   static double   last_price1 = 0, last_price2 = 0;
   bool moved_time1  = (time1  != last_time1);
   bool moved_time2  = (time2  != last_time2);
   bool moved_price1 = (price1 != last_price1);
   bool moved_price2 = (price2 != last_price2);
   double max1 = range1 * SNAP_MAX_RATIO;
   double max2 = range2 * SNAP_MAX_RATIO;
   if(moved_price1)
     {
      if(moved_time1 && !moved_time2)
        {
         double snap = candidates1[0], min_d = MathAbs(price1 - snap);
         for(int i = 1; i < 4; i++)
           {
            double d = MathAbs(price1 - candidates1[i]);
            if(d < min_d) { min_d = d; snap = candidates1[i]; }
           }
         if(min_d <= max1) ObjectSetDouble(0, boxName, OBJPROP_PRICE1, snap);
        }
      else if(!moved_time1 && moved_time2)
        {
         double snap = candidates2[0], min_d = MathAbs(price1 - snap);
         for(int i = 1; i < 4; i++)
           {
            double d = MathAbs(price1 - candidates2[i]);
            if(d < min_d) { min_d = d; snap = candidates2[i]; }
           }
         if(min_d <= max2) ObjectSetDouble(0, boxName, OBJPROP_PRICE1, snap);
        }
     }
   if(moved_price2)
     {
      if(!moved_time1 && moved_time2)
        {
         double snap = candidates2[0], min_d = MathAbs(price2 - snap);
         for(int i = 1; i < 4; i++)
           {
            double d = MathAbs(price2 - candidates2[i]);
            if(d < min_d) { min_d = d; snap = candidates2[i]; }
           }
         if(min_d <= max2) ObjectSetDouble(0, boxName, OBJPROP_PRICE2, snap);
        }
      else if(moved_time1 && !moved_time2)
        {
         double snap = candidates1[0], min_d = MathAbs(price2 - snap);
         for(int i = 1; i < 4; i++)
           {
            double d = MathAbs(price2 - candidates1[i]);
            if(d < min_d) { min_d = d; snap = candidates1[i]; }
           }
         if(min_d <= max1) ObjectSetDouble(0, boxName, OBJPROP_PRICE2, snap);
        }
     }
   last_time1  = time1;
   last_time2  = time2;
   last_price1 = ObjectGetDouble(0, boxName, OBJPROP_PRICE1);
   last_price2 = ObjectGetDouble(0, boxName, OBJPROP_PRICE2);
   g_objectsCount = ObjectsTotal();
   string extName = boxName + extSuffix;
   if(ObjectFind(0, extName) >= 0) UpdateExtendedBox(boxName);
  }

//+------------------------------------------------------------------+
//| باکس اکستند                                                       |
//+------------------------------------------------------------------+
void UpdateExtendedBox(const string boxName)
  {
   string extName = boxName + extSuffix;
   datetime time1 = (datetime)ObjectGetInteger(0, boxName, OBJPROP_TIME1);
   double price1 = ObjectGetDouble(0, boxName, OBJPROP_PRICE1);
   datetime time2 = (datetime)ObjectGetInteger(0, boxName, OBJPROP_TIME2);
   double price2 = ObjectGetDouble(0, boxName, OBJPROP_PRICE2);
   datetime rightTime = MathMax(time1, time2);
   datetime leftTime  = MathMin(time1, time2);
   int barRight = iBarShift(NULL, 0, rightTime, false);
   if(barRight < 0) return;
   double boxHigh = MathMax(price1, price2);
   double boxLow  = MathMin(price1, price2);
   int endBar = -1;
   for(int i = barRight - 1; i >= 0; i--)
     {
      double high = iHigh(NULL, 0, i);
      double low  = iLow(NULL, 0, i);
      if((high >= boxHigh && low <= boxHigh) || (high >= boxLow && low <= boxLow))
        {
         endBar = i;
         break;
        }
     }
   if(endBar == -1) endBar = 0;
   datetime endTime = iTime(NULL, 0, endBar);
   if(ObjectFind(0, extName) < 0)
     {
      ObjectCreate(0, extName, OBJ_RECTANGLE, 0, rightTime, boxHigh, endTime, boxLow);
      ObjectSetInteger(0, extName, OBJPROP_COLOR, InpBoxColor);
      ObjectSetInteger(0, extName, OBJPROP_WIDTH, 2);
      ObjectSetInteger(0, extName, OBJPROP_STYLE, InpBoxStyle);
      ObjectSetInteger(0, extName, OBJPROP_BACK, TRUE);
      ObjectSetInteger(0, extName, OBJPROP_SELECTABLE, false);
      g_lastExtName = extName;
      g_objectsCount = ObjectsTotal();
     }
   else
     {
      ObjectSetInteger(0, extName, OBJPROP_TIME1, rightTime);
      ObjectSetDouble(0, extName, OBJPROP_PRICE1, boxHigh);
      ObjectSetInteger(0, extName, OBJPROP_TIME2, endTime);
      ObjectSetDouble(0, extName, OBJPROP_PRICE2, boxLow);
     }
  }

//+------------------------------------------------------------------+
//| اکستند کردن خط افقی                                               |
//+------------------------------------------------------------------+
void UpdateExtendedLine(const string lineName)
  {
   string extName = lineName + extSuffix;
   datetime time1 = (datetime)ObjectGetInteger(0, lineName, OBJPROP_TIME1);
   double price1 = ObjectGetDouble(0, lineName, OBJPROP_PRICE1);
   datetime time2 = (datetime)ObjectGetInteger(0, lineName, OBJPROP_TIME2);
   datetime rightTime = MathMax(time1, time2);
   int barRight = iBarShift(NULL, 0, rightTime, false);
   if(barRight < 0) return;
   int endBar = -1;
   for(int i = barRight - 1; i >= 0; i--)
     {
      double high = iHigh(NULL, 0, i);
      double low  = iLow(NULL, 0, i);
      if(high >= price1 && low <= price1)
        {
         endBar = i;
         break;
        }
     }
   if(endBar == -1) endBar = 0;
   datetime endTime = iTime(NULL, 0, endBar);
   if(ObjectFind(0, extName) < 0)
     {
      ObjectCreate(0, extName, OBJ_TREND, 0, time2, price1, endTime, price1);
      ObjectSetInteger(0, extName, OBJPROP_COLOR, ObjectGetInteger(0, lineName, OBJPROP_COLOR));
      ObjectSetInteger(0, extName, OBJPROP_WIDTH, InpLineWidth);
      ObjectSetInteger(0, extName, OBJPROP_STYLE, ObjectGetInteger(0, lineName, OBJPROP_STYLE));
      ObjectSetInteger(0, extName, OBJPROP_BACK, true);
      ObjectSetInteger(0, extName, OBJPROP_SELECTABLE, FALSE);
      ObjectSetInteger(0, extName, OBJPROP_RAY_RIGHT, false);
      g_lastExtName = extName;
      g_objectsCount = ObjectsTotal();
     }
   else
     {
      ObjectSetInteger(0, extName, OBJPROP_TIME1, time2);
      ObjectSetDouble(0, extName, OBJPROP_PRICE1, price1);
      ObjectSetInteger(0, extName, OBJPROP_TIME2, endTime);
      ObjectSetDouble(0, extName, OBJPROP_PRICE2, price1);
     }
  }

//+------------------------------------------------------------------+
//| اکستنــد یا حذف اکستنــد آبجکت                                    |
//+------------------------------------------------------------------+
void ToggleExtendedObject(const string objName)
  {
   string extName = objName + extSuffix;
   if(ObjectFind(0, extName) >= 0)
     {
      ObjectDelete(0, extName);
      g_lastExtName = "";
      g_objectsCount = ObjectsTotal();
     }
   else
     {
      if(ObjectGetInteger(0, objName, OBJPROP_TYPE) == OBJ_RECTANGLE)
         UpdateExtendedBox(objName);
      else if(ObjectGetInteger(0, objName, OBJPROP_TYPE) == OBJ_TREND)
         UpdateExtendedLine(objName);
     }
   ChartRedraw();
  }

//+------------------------------------------------------------------+
//| ساخت دکمه‌های تایید باکس                                          |
//+------------------------------------------------------------------+
void CreateConfirmButtons(int x, int y)
  {
   int btnWidth = 15, btnHeight = 8, marginX = 0;
   if(ObjectCreate(0, g_confirmName, OBJ_BUTTON, 0, 0, 0) != 0)
     {
      ObjectSetInteger(0, g_confirmName, OBJPROP_XDISTANCE, x);
      ObjectSetInteger(0, g_confirmName, OBJPROP_YDISTANCE, y);
      ObjectSetInteger(0, g_confirmName, OBJPROP_XSIZE, btnWidth);
      ObjectSetInteger(0, g_confirmName, OBJPROP_YSIZE, btnHeight);
      ObjectSetInteger(0, g_confirmName, OBJPROP_CORNER, 0);
      ObjectSetString(0, g_confirmName, OBJPROP_TEXT, "Box");
      ObjectSetInteger(0, g_confirmName, OBJPROP_FONTSIZE, 5);
      ObjectSetInteger(0, g_confirmName, OBJPROP_COLOR, clrWhite);
      ObjectSetInteger(0, g_confirmName, OBJPROP_BGCOLOR, InpBoxColor);
      ObjectSetInteger(0, g_confirmName, OBJPROP_BORDER_TYPE, BORDER_RAISED);
     }
   if(ObjectCreate(0, g_highLineName, OBJ_BUTTON, 0, 0, 0) != 0)
     {
      ObjectSetInteger(0, g_highLineName, OBJPROP_XDISTANCE, x + btnWidth + marginX);
      ObjectSetInteger(0, g_highLineName, OBJPROP_YDISTANCE, y);
      ObjectSetInteger(0, g_highLineName, OBJPROP_XSIZE, btnWidth);
      ObjectSetInteger(0, g_highLineName, OBJPROP_YSIZE, btnHeight);
      ObjectSetInteger(0, g_highLineName, OBJPROP_CORNER, 0);
      ObjectSetString(0, g_highLineName, OBJPROP_TEXT, "H");
      ObjectSetInteger(0, g_highLineName, OBJPROP_FONTSIZE, 5);
      ObjectSetInteger(0, g_highLineName, OBJPROP_COLOR, clrWhite);
      ObjectSetInteger(0, g_highLineName, OBJPROP_BGCOLOR, InpHighLineColor);
      ObjectSetInteger(0, g_highLineName, OBJPROP_BORDER_TYPE, BORDER_RAISED);
     }
   if(ObjectCreate(0, g_lowLineName, OBJ_BUTTON, 0, 0, 0) != 0)
     {
      ObjectSetInteger(0, g_lowLineName, OBJPROP_XDISTANCE, x + (btnWidth + marginX) * 2);
      ObjectSetInteger(0, g_lowLineName, OBJPROP_YDISTANCE, y);
      ObjectSetInteger(0, g_lowLineName, OBJPROP_XSIZE, btnWidth);
      ObjectSetInteger(0, g_lowLineName, OBJPROP_YSIZE, btnHeight);
      ObjectSetInteger(0, g_lowLineName, OBJPROP_CORNER, 0);
      ObjectSetString(0, g_lowLineName, OBJPROP_TEXT, "L");
      ObjectSetInteger(0, g_lowLineName, OBJPROP_FONTSIZE, 5);
      ObjectSetInteger(0, g_lowLineName, OBJPROP_COLOR, clrWhite);
      ObjectSetInteger(0, g_lowLineName, OBJPROP_BGCOLOR, InpLowLineColor);
      ObjectSetInteger(0, g_lowLineName, OBJPROP_BORDER_TYPE, BORDER_RAISED);
     }
   if(ObjectCreate(0, g_openLineName, OBJ_BUTTON, 0, 0, 0) != 0)
     {
      ObjectSetInteger(0, g_openLineName, OBJPROP_XDISTANCE, x + (btnWidth + marginX) * 3);
      ObjectSetInteger(0, g_openLineName, OBJPROP_YDISTANCE, y);
      ObjectSetInteger(0, g_openLineName, OBJPROP_XSIZE, btnWidth);
      ObjectSetInteger(0, g_openLineName, OBJPROP_YSIZE, btnHeight);
      ObjectSetInteger(0, g_openLineName, OBJPROP_CORNER, 0);
      ObjectSetString(0, g_openLineName, OBJPROP_TEXT, "O");
      ObjectSetInteger(0, g_openLineName, OBJPROP_FONTSIZE, 5);
      ObjectSetInteger(0, g_openLineName, OBJPROP_COLOR, clrWhite);
      ObjectSetInteger(0, g_openLineName, OBJPROP_BGCOLOR, InpOpenLineColor);
      ObjectSetInteger(0, g_openLineName, OBJPROP_BORDER_TYPE, BORDER_RAISED);
     }
   if(ObjectCreate(0, g_closeLineName, OBJ_BUTTON, 0, 0, 0) != 0)
     {
      ObjectSetInteger(0, g_closeLineName, OBJPROP_XDISTANCE, x + (btnWidth + marginX) * 4);
      ObjectSetInteger(0, g_closeLineName, OBJPROP_YDISTANCE, y);
      ObjectSetInteger(0, g_closeLineName, OBJPROP_XSIZE, btnWidth);
      ObjectSetInteger(0, g_closeLineName, OBJPROP_YSIZE, btnHeight);
      ObjectSetInteger(0, g_closeLineName, OBJPROP_CORNER, 0);
      ObjectSetString(0, g_closeLineName, OBJPROP_TEXT, "C");
      ObjectSetInteger(0, g_closeLineName, OBJPROP_FONTSIZE, 5);
      ObjectSetInteger(0, g_closeLineName, OBJPROP_COLOR, clrWhite);
      ObjectSetInteger(0, g_closeLineName, OBJPROP_BGCOLOR, InpCloseLineColor);
      ObjectSetInteger(0, g_closeLineName, OBJPROP_BORDER_TYPE, BORDER_RAISED);
     }
   g_objectsCount = ObjectsTotal();
  }

//+------------------------------------------------------------------+
//| حذف دکمه‌های تایید باکس                                          |
//+------------------------------------------------------------------+
void DeleteConfirmButtons()
  {
   ObjectDelete(0, g_confirmName);
   ObjectDelete(0, g_highLineName);
   ObjectDelete(0, g_lowLineName);
   ObjectDelete(0, g_openLineName);
   ObjectDelete(0, g_closeLineName);
   g_selectedBar = -1;
  }

//+------------------------------------------------------------------+
//| ایجاد خط افقی بر اساس کندل                                       |
//+------------------------------------------------------------------+
void CreateHorizontalLine(int bar, int lineType)
  {
   if(bar < 0) return;
   datetime time = iTime(NULL, 0, bar);
   double price = 0;
   color  lineColor = clrWhite;
   string linePrefix = "";
   switch(lineType)
     {
      case 0:
         price = NormalizeDouble(iHigh(NULL, 0, bar), Digits);
         lineColor = InpHighLineColor;
         linePrefix = "HighLine_";
         break;
      case 1:
         price = NormalizeDouble(iLow(NULL, 0, bar), Digits);
         lineColor = InpLowLineColor;
         linePrefix = "LowLine_";
         break;
      case 2:
         price = NormalizeDouble(iOpen(NULL, 0, bar), Digits);
         lineColor = InpOpenLineColor;
         linePrefix = "OpenLine_";
         break;
      case 3:
         price = NormalizeDouble(iClose(NULL, 0, bar), Digits);
         lineColor = InpCloseLineColor;
         linePrefix = "CloseLine_";
         break;
      default:
         return;
     }
   int bar2 = bar - 10;
   if(bar2 < 0) bar2 = 0;
   datetime time2 = iTime(NULL, 0, bar2);
   string lineName = linePrefix + TimeToString(time, TIME_DATE | TIME_SECONDS);
   ObjectCreate(0, lineName, OBJ_TREND, 0, time, price, time2, price);
   ObjectSetInteger(0, lineName, OBJPROP_COLOR, lineColor);
   ObjectSetInteger(0, lineName, OBJPROP_WIDTH, InpLineWidth);
   ObjectSetInteger(0, lineName, OBJPROP_STYLE, InpBoxStyle);
   ObjectSetInteger(0, lineName, OBJPROP_BACK, true);
   ObjectSetInteger(0, lineName, OBJPROP_SELECTABLE, true);
   ObjectSetInteger(0, lineName, OBJPROP_RAY_RIGHT, false);
   g_objectsCount = ObjectsTotal();
   UpdateExtendedLine(lineName);
  }

//+------------------------------------------------------------------+
//| ایجاد باکس بر اساس کندل                                           |
//+------------------------------------------------------------------+
void CreateMainBox(int bar)
  {
   if(bar < 0) return;
   datetime t1 = iTime(NULL, 0, bar);
   double h1 = NormalizeDouble(iHigh(NULL, 0, bar), Digits);
   double l1 = NormalizeDouble(iLow(NULL, 0, bar), Digits);
   int bar2 = bar - 10;
   if(bar2 < 0) bar2 = 0;
   datetime t2 = iTime(NULL, 0, bar2);
   string boxName = boxPrefix + TimeToString(t1, TIME_DATE | TIME_SECONDS);
   if(ObjectCreate(0, boxName, OBJ_RECTANGLE, 0, t1, h1, t2, l1) != 0)
     {
      ObjectSetInteger(0, boxName, OBJPROP_COLOR, InpBoxColor);
      ObjectSetInteger(0, boxName, OBJPROP_WIDTH, 2);
      ObjectSetInteger(0, boxName, OBJPROP_STYLE, InpBoxStyle);
      ObjectSetInteger(0, boxName, OBJPROP_BACK, true);
      ObjectSetInteger(0, boxName, OBJPROP_SELECTABLE, true);
      g_lastBoxName = boxName;
      UpdateExtendedBox(boxName);
      g_objectsCount = ObjectsTotal();
     }
  }

//+------------------------------------------------------------------+
//| ایجاد خط عمودی سراسری                                           |
//+------------------------------------------------------------------+
void CreateFullVerticalLine(datetime time)
  {
   string lineName = "VLine_" + TimeToString(time, TIME_DATE | TIME_SECONDS);
   if(ObjectCreate(0, lineName, OBJ_VLINE, 0, time, 0))
     {
      ObjectSetInteger(0, lineName, OBJPROP_COLOR, InpVerticalLineColor);
      ObjectSetInteger(0, lineName, OBJPROP_WIDTH, InpLineWidth);
      ObjectSetInteger(0, lineName, OBJPROP_STYLE, InpBoxStyle);
      ObjectSetInteger(0, lineName, OBJPROP_BACK, true);
      ObjectSetInteger(0, lineName, OBJPROP_SELECTABLE, true);
      g_objectsCount = ObjectsTotal();
     }
  }

//+------------------------------------------------------------------+
//| ایجاد خط افقی سراسری                                            |
//+------------------------------------------------------------------+
void CreateFullHorizontalLine(double price)
  {
   string lineName = "HLine_" + DoubleToString(price, Digits) + "_" + IntegerToString(GetTickCount());
   if(ObjectCreate(0, lineName, OBJ_HLINE, 0, 0, price))
     {
      ObjectSetInteger(0, lineName, OBJPROP_COLOR, InpHorizontalLineColor);
      ObjectSetInteger(0, lineName, OBJPROP_WIDTH, InpLineWidth);
      ObjectSetInteger(0, lineName, OBJPROP_STYLE, InpBoxStyle);
      ObjectSetInteger(0, lineName, OBJPROP_BACK, true);
      ObjectSetInteger(0, lineName, OBJPROP_SELECTABLE, TRUE);
      g_objectsCount = ObjectsTotal();
     }
  }

//+------------------------------------------------------------------+
//| بازسازی همه اشیاء فیبوناچی بعد از تغییر تایم‌فریم               |
//+------------------------------------------------------------------+
void RecreateAllObjects()
  {
   if(PivotTime1 != 0 && PivotTime2 != 0)
     {
      CreatePivotFibonacci();
      UpdatePivotInfo();
     }
   if(SpikeTime1 != 0 && SpikeTime2 != 0)
     {
      CreateSpikeFibonacci();
      UpdateSpikeInfo();
     }
  }

//+------------------------------------------------------------------+
//| بررسی فشرده شدن کلیدهای فیبوناچی                                 |
//+------------------------------------------------------------------+
void CheckHotkeys()
  {
   if(IsKeyPressed(PivotHotkey) && PivotEnable) StartPivotDrawing();
   if(IsKeyPressed(SpikeHotkey) && SpikeEnable) StartSpikeDrawing();
  }

//+------------------------------------------------------------------+
//| شروع رسم فیبوناچی پیوت                                          |
//+------------------------------------------------------------------+
void StartPivotDrawing()
  {
   DeletePivotFibonacci();
   CreateDefaultPivotFibonacci();
  }

//+------------------------------------------------------------------+
//| شروع رسم فیبوناچی اسپایک                                         |
//+------------------------------------------------------------------+
void StartSpikeDrawing()
  {
   DeleteSpikeFibonacci();
   CreateDefaultSpikeFibonacci();
  }

//+------------------------------------------------------------------+
//| رسم پیش‌فرض فیبوناچی پیوت                                         |
//+------------------------------------------------------------------+
void CreateDefaultPivotFibonacci()
  {
   int visibleBars = (int)ChartGetInteger(0, CHART_VISIBLE_BARS);
   int firstBar    = (int)ChartGetInteger(0, CHART_FIRST_VISIBLE_BAR);
   int cornerBars  = visibleBars / 5;
   if(cornerBars < 10) cornerBars = 10;
   int highBar = iHighest(Symbol(), Period(), MODE_HIGH, cornerBars, firstBar - cornerBars + 1);
   int lowBar  = iLowest(Symbol(), Period(), MODE_LOW, cornerBars, firstBar - cornerBars + 1);
   if(highBar == -1 || lowBar == -1)
     {
      Print("Error: Could not find high/low bars");
      return;
     }
   PivotTime1  = Time[lowBar];
   PivotPrice1 = Low[lowBar];
   PivotTime2  = Time[highBar];
   PivotPrice2 = High[highBar];
   if(PivotTime2 < PivotTime1)
     {
      datetime tempTime  = PivotTime1;
      double   tempPrice = PivotPrice1;
      PivotTime1  = PivotTime2;
      PivotPrice1 = PivotPrice2;
      PivotTime2  = tempTime;
      PivotPrice2 = tempPrice;
     }
   CreatePivotFibonacci();
  }

//+------------------------------------------------------------------+
//| رسم پیش‌فرض فیبوناچی اسپایک                                      |
//+------------------------------------------------------------------+
void CreateDefaultSpikeFibonacci()
  {
   int visibleBars = (int)ChartGetInteger(0, CHART_VISIBLE_BARS);
   int firstBar    = (int)ChartGetInteger(0, CHART_FIRST_VISIBLE_BAR);
   int cornerBars  = visibleBars / 5;
   if(cornerBars < 10) cornerBars = 10;
   int highBar = iHighest(Symbol(), Period(), MODE_HIGH, cornerBars, firstBar - cornerBars + 1);
   int lowBar  = iLowest(Symbol(), Period(), MODE_LOW, cornerBars, firstBar - cornerBars + 1);
   if(highBar == -1 || lowBar == -1)
     {
      Print("Error: Could not find high/low bars");
      return;
     }
   SpikeTime1  = Time[lowBar];
   SpikePrice1 = Low[lowBar];
   SpikeTime2  = Time[highBar];
   SpikePrice2 = High[highBar];
   if(SpikeTime2 < SpikeTime1)
     {
      datetime tempTime  = SpikeTime1;
      double   tempPrice = SpikePrice1;
      SpikeTime1  = SpikeTime2;
      SpikePrice1 = SpikePrice2;
      SpikeTime2  = tempTime;
      SpikePrice2 = tempPrice;
     }
   CreateSpikeFibonacci();
  }

//+------------------------------------------------------------------+
//| ایجاد فیبوناچی پیوت                                              |
//+------------------------------------------------------------------+
void CreatePivotFibonacci()
  {
   string baseName = PivotObjectPrefix + "Base";
   if(ObjectFind(0, baseName) >= 0) ObjectDelete(0, baseName);
   if(!ObjectCreate(0, baseName, OBJ_FIBO, 0, PivotTime1, PivotPrice1, PivotTime2, PivotPrice2))
     {
      Print("Error creating Pivot Fibonacci: ", GetLastError());
      return;
     }
   ObjectSetInteger(0, baseName, OBJPROP_COLOR, PivotColor);
   ObjectSetInteger(0, baseName, OBJPROP_STYLE, PivotStyle);
   ObjectSetInteger(0, baseName, OBJPROP_WIDTH, PivotWidth);
   ObjectSetInteger(0, baseName, OBJPROP_BACK, false);
   ObjectSetInteger(0, baseName, OBJPROP_SELECTABLE, true);
   ObjectSetInteger(0, baseName, OBJPROP_SELECTED, true);
   ObjectSetInteger(0, baseName, OBJPROP_RAY_RIGHT, false);
   ObjectSetInteger(0, baseName, OBJPROP_TIMEFRAMES, 
                     OBJ_PERIOD_M1 | OBJ_PERIOD_M5 | OBJ_PERIOD_M15 | OBJ_PERIOD_M30 |
                     OBJ_PERIOD_H1 | OBJ_PERIOD_H4 | OBJ_PERIOD_D1  | OBJ_PERIOD_W1  | OBJ_PERIOD_MN1);
   AddPivotLevels(baseName);
   Print("Pivot Fibonacci created successfully with ", ObjectGetInteger(0, baseName, OBJPROP_LEVELS), " levels");
   ChartRedraw();
  }

//+------------------------------------------------------------------+
//| ایجاد فیبوناچی اسپایک                                             |
//+------------------------------------------------------------------+
void CreateSpikeFibonacci()
  {
   string baseName = SpikeObjectPrefix + "Base";
   if(ObjectFind(0, baseName) >= 0) ObjectDelete(0, baseName);
   if(!ObjectCreate(0, baseName, OBJ_FIBO, 0, SpikeTime1, SpikePrice1, SpikeTime2, SpikePrice2))
     {
      Print("Error creating Spike Fibonacci: ", GetLastError());
      return;
     }
   ObjectSetInteger(0, baseName, OBJPROP_COLOR, SpikeColor);
   ObjectSetInteger(0, baseName, OBJPROP_STYLE, SpikeStyle);
   ObjectSetInteger(0, baseName, OBJPROP_WIDTH, SpikeWidth);
   ObjectSetInteger(0, baseName, OBJPROP_BACK, false);
   ObjectSetInteger(0, baseName, OBJPROP_SELECTABLE, true);
   ObjectSetInteger(0, baseName, OBJPROP_SELECTED, true);
   ObjectSetInteger(0, baseName, OBJPROP_RAY_RIGHT, false);
   ObjectSetInteger(0, baseName, OBJPROP_TIMEFRAMES,
                     OBJ_PERIOD_M1 | OBJ_PERIOD_M5 | OBJ_PERIOD_M15 | OBJ_PERIOD_M30 |
                     OBJ_PERIOD_H1 | OBJ_PERIOD_H4 | OBJ_PERIOD_D1  | OBJ_PERIOD_W1  | OBJ_PERIOD_MN1);
   AddSpikeLevels(baseName);
   Print("Spike Fibonacci created successfully with ", ObjectGetInteger(0, baseName, OBJPROP_LEVELS), " levels");
   ChartRedraw();
  }

//+------------------------------------------------------------------+
//| افزودن سطح‌های پیوت فیبوناچی                                     |
//+------------------------------------------------------------------+
void AddPivotLevels(string objectName)
  {
   int levelCount = 0;
   ObjectSetInteger(0, objectName, OBJPROP_LEVELS, 0);
   if(PivotLevel_0_Enable)
     {
      ObjectSetInteger(0, objectName, OBJPROP_LEVELS, levelCount + 1);
      AddFibonacciLevel(objectName, levelCount++, PivotLevel_0_Value/100.0, PivotLevelColor, DoubleToString(PivotLevel_0_Value,1)+"%");
     }
   if(PivotLevel_33_Enable)
     {
      ObjectSetInteger(0, objectName, OBJPROP_LEVELS, levelCount + 1);
      AddFibonacciLevel(objectName, levelCount++, PivotLevel_33_Value/100.0, PivotLevel_33_Color, DoubleToString(PivotLevel_33_Value,1)+"%");
     }
   if(PivotLevel_50_Enable)
     {
      ObjectSetInteger(0, objectName, OBJPROP_LEVELS, levelCount + 1);
      AddFibonacciLevel(objectName, levelCount++, PivotLevel_50_Value/100.0, PivotLevelColor, DoubleToString(PivotLevel_50_Value,1)+"%");
     }
   if(PivotLevel_66_Enable)
     {
      ObjectSetInteger(0, objectName, OBJPROP_LEVELS, levelCount + 1);
      AddFibonacciLevel(objectName, levelCount++, PivotLevel_66_Value/100.0, PivotLevelColor, DoubleToString(PivotLevel_66_Value,1)+"%");
     }
   if(PivotLevel_100_Enable)
     {
      ObjectSetInteger(0, objectName, OBJPROP_LEVELS, levelCount + 1);
      AddFibonacciLevel(objectName, levelCount++, PivotLevel_100_Value/100.0, PivotLevelColor, DoubleToString(PivotLevel_100_Value,1)+"%");
     }
   if(PivotLevel_122_Enable)
     {
      ObjectSetInteger(0, objectName, OBJPROP_LEVELS, levelCount + 1);
      AddFibonacciLevel(objectName, levelCount++, PivotLevel_122_Value/100.0, PivotLevelColor, DoubleToString(PivotLevel_122_Value,1)+"%");
     }
   if(PivotLevel_133_Enable)
     {
      ObjectSetInteger(0, objectName, OBJPROP_LEVELS, levelCount + 1);
      AddFibonacciLevel(objectName, levelCount++, PivotLevel_133_Value/100.0, PivotLevelColor, DoubleToString(PivotLevel_133_Value,1)+"%");
     }
   if(PivotLevel_155_Enable)
     {
      ObjectSetInteger(0, objectName, OBJPROP_LEVELS, levelCount + 1);
      AddFibonacciLevel(objectName, levelCount++, PivotLevel_155_Value/100.0, PivotLevelColor, DoubleToString(PivotLevel_155_Value,1)+"%");
     }
   if(PivotLevel_166_Enable)
     {
      ObjectSetInteger(0, objectName, OBJPROP_LEVELS, levelCount + 1);
      AddFibonacciLevel(objectName, levelCount++, PivotLevel_166_Value/100.0, PivotLevelColor, DoubleToString(PivotLevel_166_Value,1)+"%");
     }
   if(PivotLevel_188_Enable)
     {
      ObjectSetInteger(0, objectName, OBJPROP_LEVELS, levelCount + 1);
      AddFibonacciLevel(objectName, levelCount++, PivotLevel_188_Value/100.0, PivotLevelColor, DoubleToString(PivotLevel_188_Value,1)+"%");
     }
   if(PivotLevel_199_Enable)
     {
      ObjectSetInteger(0, objectName, OBJPROP_LEVELS, levelCount + 1);
      AddFibonacciLevel(objectName, levelCount++, PivotLevel_199_Value/100.0, PivotLevelColor, DoubleToString(PivotLevel_199_Value,1)+"%");
     }
   if(PivotLevel_4_Enable)
     {
      ObjectSetInteger(0, objectName, OBJPROP_LEVELS, levelCount + 1);
      AddFibonacciLevel(objectName, levelCount++, PivotLevel_4_Value/100.0, PivotLevelColor, DoubleToString(PivotLevel_4_Value,1)+"%");
     }
   if(PivotLevel_130_Enable)
     {
      ObjectSetInteger(0, objectName, OBJPROP_LEVELS, levelCount + 1);
      AddFibonacciLevel(objectName, levelCount++, PivotLevel_130_Value/100.0, PivotLevelColor, DoubleToString(PivotLevel_130_Value,1)+"%");
     }
   if(PivotLevel_4236_Enable)
     {
      ObjectSetInteger(0, objectName, OBJPROP_LEVELS, levelCount + 1);
      AddFibonacciLevel(objectName, levelCount++, PivotLevel_4236_Value/100.0, PivotLevelColor, DoubleToString(PivotLevel_4236_Value,1)+"%");
     }
   if(PivotLevel_Custom_Enable)
     {
      ObjectSetInteger(0, objectName, OBJPROP_LEVELS, levelCount + 1);
      AddFibonacciLevel(objectName, levelCount++, PivotLevel_Custom_Value/100.0, PivotLevelColor, DoubleToString(PivotLevel_Custom_Value,1)+"%");
     }
   Print("Added ", levelCount, " Pivot levels");
  }

//+------------------------------------------------------------------+
//| افزودن سطح‌های اسپایک فیبوناچی                                   |
//+------------------------------------------------------------------+
void AddSpikeLevels(string objectName)
  {
   int levelCount = 0;
   ObjectSetInteger(0, objectName, OBJPROP_LEVELS, 0);
   if(SpikeLevel_0_Enable)
     {
      ObjectSetInteger(0, objectName, OBJPROP_LEVELS, levelCount + 1);
      AddFibonacciLevel(objectName, levelCount++, SpikeLevel_0_Value/100.0, SpikeLevelColor, DoubleToString(SpikeLevel_0_Value,1)+"%");
     }
   if(SpikeLevel_33_Enable)
     {
      ObjectSetInteger(0, objectName, OBJPROP_LEVELS, levelCount + 1);
      AddFibonacciLevel(objectName, levelCount++, SpikeLevel_33_Value/100.0, SpikeLevel_33_Color, DoubleToString(SpikeLevel_33_Value,1)+"%");
     }
   if(SpikeLevel_50_Enable)
     {
      ObjectSetInteger(0, objectName, OBJPROP_LEVELS, levelCount + 1);
      AddFibonacciLevel(objectName, levelCount++, SpikeLevel_50_Value/100.0, SpikeLevelColor, DoubleToString(SpikeLevel_50_Value,1)+"%");
     }
   if(SpikeLevel_66_Enable)
     {
      ObjectSetInteger(0, objectName, OBJPROP_LEVELS, levelCount + 1);
      AddFibonacciLevel(objectName, levelCount++, SpikeLevel_66_Value/100.0, SpikeLevelColor, DoubleToString(SpikeLevel_66_Value,1)+"%");
     }
   if(SpikeLevel_100_Enable)
     {
      ObjectSetInteger(0, objectName, OBJPROP_LEVELS, levelCount + 1);
      AddFibonacciLevel(objectName, levelCount++, SpikeLevel_100_Value/100.0, SpikeLevelColor, DoubleToString(SpikeLevel_100_Value,1)+"%");
     }
   if(SpikeLevel_122_Enable)
     {
      ObjectSetInteger(0, objectName, OBJPROP_LEVELS, levelCount + 1);
      AddFibonacciLevel(objectName, levelCount++, SpikeLevel_122_Value/100.0, SpikeLevel_Steps_Color, DoubleToString(SpikeLevel_122_Value,1)+"%");
     }
   if(SpikeLevel_133_Enable)
     {
      ObjectSetInteger(0, objectName, OBJPROP_LEVELS, levelCount + 1);
      AddFibonacciLevel(objectName, levelCount++, SpikeLevel_133_Value/100.0, SpikeLevel_Steps_Color, DoubleToString(SpikeLevel_133_Value,1)+"%");
     }
   if(SpikeLevel_155_Enable)
     {
      ObjectSetInteger(0, objectName, OBJPROP_LEVELS, levelCount + 1);
      AddFibonacciLevel(objectName, levelCount++, SpikeLevel_155_Value/100.0, SpikeLevel_Steps_Color, DoubleToString(SpikeLevel_155_Value,1)+"%");
     }
   if(SpikeLevel_166_Enable)
     {
      ObjectSetInteger(0, objectName, OBJPROP_LEVELS, levelCount + 1);
      AddFibonacciLevel(objectName, levelCount++, SpikeLevel_166_Value/100.0, SpikeLevel_Steps_Color, DoubleToString(SpikeLevel_166_Value,1)+"%");
     }
   if(SpikeLevel_188_Enable)
     {
      ObjectSetInteger(0, objectName, OBJPROP_LEVELS, levelCount + 1);
      AddFibonacciLevel(objectName, levelCount++, SpikeLevel_188_Value/100.0, SpikeLevel_Steps_Color, DoubleToString(SpikeLevel_188_Value,1)+"%");
     }
   if(SpikeLevel_199_Enable)
     {
      ObjectSetInteger(0, objectName, OBJPROP_LEVELS, levelCount + 1);
      AddFibonacciLevel(objectName, levelCount++, SpikeLevel_199_Value/100.0, SpikeLevel_Steps_Color, DoubleToString(SpikeLevel_199_Value,1)+"%");
     }
   if(SpikeLevel_4_Enable)
     {
      ObjectSetInteger(0, objectName, OBJPROP_LEVELS, levelCount + 1);
      AddFibonacciLevel(objectName, levelCount++, SpikeLevel_4_Value/100.0, SpikeLevel_Steps_Color, DoubleToString(SpikeLevel_4_Value,1)+"%");
     }
   if(SpikeLevel_130_Enable)
     {
      ObjectSetInteger(0, objectName, OBJPROP_LEVELS, levelCount + 1);
      AddFibonacciLevel(objectName, levelCount++, SpikeLevel_130_Value/100.0, SpikeLevel_Steps_Color, DoubleToString(SpikeLevel_130_Value,1)+"%");
     }
   if(SpikeLevel_4236_Enable)
     {
      ObjectSetInteger(0, objectName, OBJPROP_LEVELS, levelCount + 1);
      AddFibonacciLevel(objectName, levelCount++, SpikeLevel_4236_Value/100.0, SpikeLevel_Steps_Color, DoubleToString(SpikeLevel_4236_Value,1)+"%");
     }
   if(SpikeLevel_Custom_Enable)
     {
      ObjectSetInteger(0, objectName, OBJPROP_LEVELS, levelCount + 1);
      AddFibonacciLevel(objectName, levelCount++, SpikeLevel_Custom_Value/100.0, SpikeLevelColor, DoubleToString(SpikeLevel_Custom_Value,1)+"%");
     }
   Print("Added ", levelCount, " Spike levels");
  }

//+------------------------------------------------------------------+
//| افزودن یک سطح فیبوناچی                                          |
//+------------------------------------------------------------------+
void AddFibonacciLevel(string objectName, int levelIndex, double levelValue, color levelColor, string levelText)
  {
   ObjectSetDouble(0, objectName, OBJPROP_LEVELVALUE, levelIndex, levelValue);
   ObjectSetInteger(0, objectName, OBJPROP_LEVELCOLOR, levelIndex, levelColor);
   ObjectSetString(0, objectName, OBJPROP_LEVELTEXT, levelIndex, levelText);
   ObjectSetInteger(0, objectName, OBJPROP_LEVELSTYLE, levelIndex, STYLE_DASHDOTDOT);
   ObjectSetInteger(0, objectName, OBJPROP_LEVELWIDTH, levelIndex, 1);
   Print("Level ", levelIndex, ": ", DoubleToString(levelValue,4), " (", levelText, ")");
  }

//+------------------------------------------------------------------+
//| به‌روز کردن فیبوناچی پیوت                                         |
//+------------------------------------------------------------------+
void UpdatePivotFibonacci()
  {
   string baseName = PivotObjectPrefix + "Base";
   if(ObjectFind(0, baseName) >= 0)
     {
      PivotTime1  = (datetime)ObjectGetInteger(0, baseName, OBJPROP_TIME, 0);
      PivotPrice1 = ObjectGetDouble(0, baseName, OBJPROP_PRICE, 0);
      PivotTime2  = (datetime)ObjectGetInteger(0, baseName, OBJPROP_TIME, 1);
      PivotPrice2 = ObjectGetDouble(0, baseName, OBJPROP_PRICE, 1);
      if(PivotTime2 < PivotTime1)
        {
         datetime tempTime  = PivotTime1;
         double   tempPrice = PivotPrice1;
         PivotTime1  = PivotTime2;
         PivotPrice1 = PivotPrice2;
         PivotTime2  = tempTime;
         PivotPrice2 = tempPrice;
         ObjectMove(0, baseName, 0, PivotTime1, PivotPrice1);
         ObjectMove(0, baseName, 1, PivotTime2, PivotPrice2);
         UpdatePivotInfo();
        }
     }
  }

//+------------------------------------------------------------------+
//| به‌روز کردن فیبوناچی اسپایک                                       |
//+------------------------------------------------------------------+
void UpdateSpikeFibonacci()
  {
   string baseName = SpikeObjectPrefix + "Base";
   if(ObjectFind(0, baseName) >= 0)
     {
      SpikeTime1  = (datetime)ObjectGetInteger(0, baseName, OBJPROP_TIME, 0);
      SpikePrice1 = ObjectGetDouble(0, baseName, OBJPROP_PRICE, 0);
      SpikeTime2  = (datetime)ObjectGetInteger(0, baseName, OBJPROP_TIME, 1);
      SpikePrice2 = ObjectGetDouble(0, baseName, OBJPROP_PRICE, 1);
      if(SpikeTime2 < SpikeTime1)
        {
         datetime tempTime  = SpikeTime1;
         double   tempPrice = SpikePrice1;
         SpikeTime1  = SpikeTime2;
         SpikePrice1 = SpikePrice2;
         SpikeTime2  = tempTime;
         SpikePrice2 = tempPrice;
         ObjectMove(0, baseName, 0, SpikeTime1, SpikePrice1);
         ObjectMove(0, baseName, 1, SpikeTime2, SpikePrice2);
         UpdateSpikeInfo();
        }
     }
  }

//+------------------------------------------------------------------+
//| حذف فیبوناچی پیوت                                                |
//+------------------------------------------------------------------+
void DeletePivotFibonacci()
  {
   string baseName = PivotObjectPrefix + "Base";
   if(ObjectFind(0, baseName) >= 0) ObjectDelete(0, baseName);
   ChartRedraw();
  }

//+------------------------------------------------------------------+
//| حذف فیبوناچی اسپایک                                               |
//+------------------------------------------------------------------+
void DeleteSpikeFibonacci()
  {
   string baseName = SpikeObjectPrefix + "Base";
   if(ObjectFind(0, baseName) >= 0) ObjectDelete(0, baseName);
   ChartRedraw();
  }

//+------------------------------------------------------------------+
//| به‌روز کردن اطلاعات فیبوناچی                                     |
//+------------------------------------------------------------------+
void UpdateFibonacciInfo()
  {
   UpdatePivotInfo();
   UpdateSpikeInfo();
  }

//+------------------------------------------------------------------+
//| نمایش اطلاعات پیوت                                              |
//+------------------------------------------------------------------+
void UpdatePivotInfo()
  {
   string pivotBaseName = PivotObjectPrefix + "Base";
   string labelName     = "PivotInfo_" + IntegerToString(ChartID());
   if(ObjectFind(0, pivotBaseName) >= 0)
     {
      double pivotHeight = MathAbs(PivotPrice2 - PivotPrice1) / 10.0;
      int    pivotHeightPips = (int)(pivotHeight / Point);
      string info = "PIVOT: (" + IntegerToString(pivotHeightPips) + " pips)";
      if(ObjectFind(0, labelName) < 0)
        {
         ObjectCreate(0, labelName, OBJ_LABEL, 0, 0, 0);
         ObjectSetInteger(0, labelName, OBJPROP_COLOR, PivotColor);
         ObjectSetInteger(0, labelName, OBJPROP_FONTSIZE, 10);
         ObjectSetString(0, labelName, OBJPROP_FONT, "Arial Bold");
         ObjectSetInteger(0, labelName, OBJPROP_BACK, false);
         ObjectSetInteger(0, labelName, OBJPROP_SELECTABLE, false);
         ObjectSetInteger(0, labelName, OBJPROP_TIMEFRAMES, OBJ_ALL_PERIODS);
         ObjectSetInteger(0, labelName, OBJPROP_CORNER, CORNER_LEFT_UPPER);
         ObjectSetInteger(0, labelName, OBJPROP_ANCHOR, ANCHOR_LEFT_UPPER);
        }
      ObjectSetInteger(0, labelName, OBJPROP_XDISTANCE, 10);
      ObjectSetInteger(0, labelName, OBJPROP_YDISTANCE, 30);
      ObjectSetString(0, labelName, OBJPROP_TEXT, info);
     }
   else if(ObjectFind(0, labelName) >= 0)
     ObjectDelete(0, labelName);
  }

//+------------------------------------------------------------------+
//| نمایش اطلاعات اسپایک                                              |
//+------------------------------------------------------------------+
void UpdateSpikeInfo()
  {
   string spikeBaseName = SpikeObjectPrefix + "Base";
   string labelName     = "SpikeInfo_" + IntegerToString(ChartID());
   if(ObjectFind(0, spikeBaseName) >= 0)
     {
      double spikeHeight = MathAbs(SpikePrice2 - SpikePrice1);
      int    spikeHeightPips = (int)(spikeHeight / Point * 0.1);
      string info = "SPIKE: (" + IntegerToString(spikeHeightPips) + " pips)";
      if(ObjectFind(0, labelName) < 0)
        {
         ObjectCreate(0, labelName, OBJ_LABEL, 0, 0, 0);
         ObjectSetInteger(0, labelName, OBJPROP_COLOR, SpikeColor);
         ObjectSetInteger(0, labelName, OBJPROP_FONTSIZE, 10);
         ObjectSetString(0, labelName, OBJPROP_FONT, "Arial Bold");
         ObjectSetInteger(0, labelName, OBJPROP_BACK, false);
         ObjectSetInteger(0, labelName, OBJPROP_SELECTABLE, false);
         ObjectSetInteger(0, labelName, OBJPROP_TIMEFRAMES, OBJ_ALL_PERIODS);
         ObjectSetInteger(0, labelName, OBJPROP_CORNER, CORNER_LEFT_UPPER);
         ObjectSetInteger(0, labelName, OBJPROP_ANCHOR, ANCHOR_LEFT_UPPER);
        }
      ObjectSetInteger(0, labelName, OBJPROP_XDISTANCE, 10);
      ObjectSetInteger(0, labelName, OBJPROP_YDISTANCE, 50);
      ObjectSetString(0, labelName, OBJPROP_TEXT, info);
     }
   else if(ObjectFind(0, labelName) >= 0)
     ObjectDelete(0, labelName);
  }

//+------------------------------------------------------------------+
//| دریافت قیمت سطح فیبوناچی                                          |
//+------------------------------------------------------------------+
double GetFibonacciLevelPrice(string objectName, double levelValue)
  {
   double price1 = ObjectGetDouble(0, objectName, OBJPROP_PRICE, 0);
   double price2 = ObjectGetDouble(0, objectName, OBJPROP_PRICE, 1);
   return price1 + (price2 - price1) * levelValue;
  }
  //+------------------------------------------------------------------+
//| بررسی تغییرات چارت (بهینه شده برای سرعت)                          |
//+------------------------------------------------------------------+
bool HasChartChanged()
{
    int currentObjectsCount = ObjectsTotal();
    
    // بررسی سریع تعداد اشیاء
    if(currentObjectsCount != LastObjectsCount)
    {
        LastObjectsCount = currentObjectsCount;
        if(EnableVerboseLogging)
            Print("تغییر تعداد اشیاء: ", currentObjectsCount);
        return true;
    }
    
    // بررسی hash فقط اگر تعداد اشیاء کم باشد (برای سرعت)
    if(currentObjectsCount <= 20)
    {
        string currentHash = GenerateObjectsHash();
        if(currentHash != LastObjectsHash)
        {
            LastObjectsHash = currentHash;
            if(EnableVerboseLogging)
                Print("تغییر در اشیاء شناسایی شد");
            return true;
        }
    }
    
    return false;
}
//+------------------------------------------------------------------+
//| تولید Hash سبک و سریع برای اشیاء                                  |
//+------------------------------------------------------------------+
string GenerateObjectsHash()
{
    string hash = "";
    int total = ObjectsTotal();
    
    // محدود کردن بررسی برای سرعت بالا
    int checkLimit = MathMin(total, 5);
    
    for(int i = 0; i < checkLimit; i++)
    {
        string objName = ObjectName(i);
        if(objName != "")
        {
            // Hash ساده بر اساس نام و موقعیت
            hash += StringSubstr(objName, 0, 3) + IntegerToString(i);
        }
    }
    
    return hash;
}

//+------------------------------------------------------------------+
//| ذخیره تمپلیت فعلی                                                 |
//+------------------------------------------------------------------+
void SaveCurrentTemplate()
{
    if(CurrentTemplateName == "" || IsShuttingDown) return;
    
    // ذخیره تمپلیت
    bool saveResult = ChartSaveTemplate(0, CurrentTemplateName + ".tpl");
    
    if(saveResult)
    {
        // پیام محدود برای جلوگیری از spam
        static datetime lastSuccessMsg = 0;
        if(EnableVerboseLogging || TimeCurrent() - lastSuccessMsg > 60)
        {
            Print(" تمپلیت ذخیره شد: ", CurrentTemplateName, ".tpl");
            lastSuccessMsg = TimeCurrent();
        }
    }
    else
    {
        Print(" خطا در ذخیره تمپلیت: ", CurrentTemplateName, ".tpl");
    }
}

//+------------------------------------------------------------------+
