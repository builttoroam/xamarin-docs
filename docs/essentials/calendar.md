---
title: "Xamarin.Essentials: Calendar"
description: "This document describes the Calendar class in Xamarin.Essentials, which lets you view the user's calendar and event information."
ms.assetid: a6d5f2a2-0f49-4726-9a74-8e7667ddb127
author: ScottBTR
ms.author: ScottYoung-5180
ms.date: 03/12/2019
---

# Xamarin.Essentials: Calendar

The **Calendar** class lets you view the user's calendar and event information.

## Get started

[!include[](~/essentials/includes/get-started.md)]

To access the **Calendar** functionality the following platform specific setup is required.

# [Android](#tab/android)

The `Read Calendar` and `Write Calendar` permission is required and must be configured in the Android project. 

Open the **AndroidManifest.xml** file under the **Properties** folder and add the following inside of the **manifest** node.

```xml
<uses-permission android:name="android.permission.READ_CALENDAR" />
<uses-permission android:name="android.permission.WRITE_CALENDAR" />
```

Or right click on the Android project and open the project's properties. Under **Android Manifest** find the **Required permissions:** area and check the **READ_CALENDAR** and **WRITE_CALENDAR** permission. This will automatically update the **AndroidManifest.xml** file.

# [iOS](#tab/ios)

Your app's **Info.plist** must contain the `NSCalendarsUsageDescription` and the `NSRemindersUsageDescription` key in order to access the device’s calendars & reminders.

Open the plist editor and add the **Privacy - Calendars Usage Description** and the **Privacy - Reminders Usage Description** property and fill in a value to display to the user.

Or manually edit the file and add the following and update the rationale:

```xml
<key>NSCalendarsUsageDescription</key>
<string>Fill in a reason why your app needs access to calendars.</string>
<key>NSRemindersUsageDescription</key>
<string>Fill in a reason why your app needs access to reminders.</string>
```

# [UWP](#tab/uwp)

You must set the `Appointments` permission for the application. This can be done by opening the **Package.appxmanifest** and selecting the **Capabilities** tab and checking **Appointments**.

-----

## Using Calendar

Add a reference to Xamarin.Essentials in your class:

```csharp
using Xamarin.Essentials;
```

Get all existing calendars:

```csharp
var calendars = await Calendar.GetCalendarsAsync(); 
// The var calendars will now hold a list of all existing/retrievable calendars. (List<DeviceCalendar>)
```

Alter Default start date for event retrieval: 
```
> [!NOTE]
> // This will mean requests without a start date passed in, events will be retrieved starting from 7 days ago.
```
Note: The default start date when not altered in TimeSpan.Zero (retrieve forward from DateTime.Now)
```csharp
var newDefaultStartTime = new TimeSpan(-7, 0, 0, 0);
Calendar.SetDefaultStartTimeOffset(newDefaultStartTime); 
```

Alter Default end date for event retrieval: 
```
> [!NOTE]
> The default end date when not altered in TimeSpan.FromDays(14) (retrieve until 14 days after the start date)
```
```csharp
var newDefaultEndDate = new TimeSpan(7, 0, 0, 0);
Calendar.SetDefaultEndTimeOffset(newDefaultEndDate); 
// This will mean requests without a end date passed, events will be retrieved until from 7 days after the start date.
```


Get all existing events for all calendars using default date range:
```
> [!TIP]
> When pulling back events they use an 'overlapping' formula, i.e. if I had an event that went from 9:00 - 10:00 and my start or end time intersects that time slot it will be included in the resulting list. 
```
```csharp
var events =  await Calendar.GetEventsAsync(); 
// The var events will now hold a list of existing/retrievable events for all calendars. (List<DeviceEvent>)
```

Get all existing events for a specific calendar using default date range:

```csharp
using System.Linq;

try
{
	var calendarId = (await Calendar.GetCalendarsAsync()).First().Id;
	var events =  await Calendar.GetEventsAsync(calendarId); 
	// The var events will now hold a list of existing/retrievable events for our first calendar. (List<DeviceEvent>)
}
catch (ArgumentNullException)
{
	// Unable to get any calendars
}
```


Get all existing events for all calendar that overlap the provided start date and a default end date:

```csharp
var startDate = DateTimeOffset.Now.
var events =  await Calendar.GetEventsAsync(startDate: DateTimeOffset.Now.AddDays(14)); 
// The var events will now hold a list of existing/retrievable events for all calendars where events overlap 14 days from now until the default end date. (List<DeviceEvent>)
```

Get all existing events for a specific calendar that overlap a start & end date:

```csharp
using System.Linq;

try
{
	var calendarId = (await Calendar.GetCalendarsAsync()).First().Id;
	var sDate = new DateTimeOffset(2019, 4, 10, 12, 0, 0, TimeSpan.Zero);
	var eDate = new DateTimeOffset(2019, 4, 10, 13, 0, 0, TimeSpan.Zero);
	var events = await Calendar.GetEventsAsync(calendarId, sDate, eDate); 
	// the var events will now hold a list of existing/retrievable events for our first calendar. where the events timeslot overlaps 2019-04-10 12:00 pm - 2019-04-10 1:00 pm
}
catch (ArgumentNullException)
{
	// Unable to get any calendars
}
```

## Platform Differences

# [Android](#tab/android)

- Android stores it's `All Day` events in UTC time zone despite where it was created.

# [iOS](#tab/ios)

- iOS can not request longer than a 4 year time span at once, in this situation the first 4 years will be retrieved.

# [UWP](#tab/uwp)

No platform differences.

-----

## API

- [Calendar source code](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Calendar)
- [Calendar API documentation](xref:Xamarin.Essentials.Calendar)

