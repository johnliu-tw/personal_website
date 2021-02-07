---
layout: post
title: 'Asp.net - Static 型態變數之雷'
date: 2017-11-20 08:50
comments: true
categories:
- Asp.net
---
前一陣子在開發時，發現有個 Bug 是在特定某頁，
B 使用者會使用到 A 使用者的身份留言。
這麼嚴重的問題當然就當天開始hotfix....
因此找了一陣子後，發現是靜態變數的問題。
雖然說這是在開發 Asp.net C# 專案時遇到的問題，
但這個問題的核心知識是在不同語言的開發專案都會遇到的。
static 變數是有著 "全域變數" 的概念，因此會把資料存在 Server 端，
任何人呼叫該變數時，取的資料都是**同一個**。

我們原始的code是如此：
```csharp
    public partial class SpecialTripDetail : System.Web.UI.Page
    {
    		static protected LoggedInUser loggedInUser = null;
            
        protected void Page_Load(object sender, EventArgs e)
        {
         	loggedInUser = (LoggedInUser)Session["user"];
          ...
          
          [WebMethod]
        	public static bool DeletedMember(string journeyId)
        	{
              string planId = SpecialTripService.FindJoinedPlan(journeyId, loggedInUser.id);
              ...
          }
        }
    
    }
```

之前的工程師可能沒有想清楚，想說在做 Web method 讓前台 Ajax 打時，
可以直接 call 用 loggedInUser 的 id，編譯會過，測試也會過嘖嘖....
但這樣下一個使用者如果沒有複寫，就使用到該 static 變數，就會用到別人的資料。

因此簡單改寫成這樣:
```csharp
    public partial class SpecialTripDetail : System.Web.UI.Page
    {
    		protected LoggedInUser loggedInUser = null;
            
        protected void Page_Load(object sender, EventArgs e)
        {
         	loggedInUser = (LoggedInUser)Session["user"];
          ...
          
          [WebMethod]
        	public static bool DeletedMember(string journeyId, string memberId)
        	{
              string planId = SpecialTripService.FindJoinedPlan(journeyId, memberId);
              ...
          }
        }
    
    }
```

讓前端 Ajax 打時多送一個參數即可，
順便提醒自己未來 CodeReview 時要更全面性的檢視問題。