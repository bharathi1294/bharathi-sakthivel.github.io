---
title: "Grouping Actions as Menu Button in CDS"
date: 2025-11-10 08:00:00 +0530
categories: [CDS]
tags: [cds, abap, annotations, fiori]
---

While exploring, one annotation caught my eye: `#𝗙𝗢𝗥_𝗔𝗖𝗧𝗜𝗢𝗡_𝗚𝗥𝗢𝗨𝗣`. I looked into it further and found that it allows you to group multiple actions under a single menu! This is particularly helpful when you want to group actions either on the List Report or the Object Page.

For example, here I’ve grouped two actions (acceptTravel and rejectTravel) under a single menu labeled "Change Status":\

`@𝗨𝗜: {`\
 `𝗹𝗶𝗻𝗲𝗜𝘁𝗲𝗺: [`\
 `{ 𝗽𝗼𝘀𝗶𝘁𝗶𝗼𝗻: 𝟭𝟬, 𝗶𝗺𝗽𝗼𝗿𝘁𝗮𝗻𝗰𝗲: #𝗛𝗜𝗚𝗛 },`\
 `{ 𝘁𝘆𝗽𝗲: #𝗙𝗢𝗥_𝗔𝗖𝗧𝗜𝗢𝗡_𝗚𝗥𝗢𝗨𝗣, 𝗹𝗮𝗯𝗲𝗹: '𝗖𝗵𝗮𝗻𝗴𝗲 𝗦𝘁𝗮𝘁𝘂𝘀', 𝗮𝗰𝘁𝗶𝗼𝗻𝗚𝗿𝗼𝘂𝗽𝗜𝗱: ``'𝗺𝗲𝗻𝘂-𝗖𝗵𝗮𝗻𝗴𝗲𝗦𝘁𝗮𝘁𝘂𝘀' },`\
 `{ 𝘁𝘆𝗽𝗲: #𝗙𝗢𝗥_𝗔𝗖𝗧𝗜𝗢𝗡, 𝗱𝗮𝘁𝗮𝗔𝗰𝘁𝗶𝗼𝗻: '𝗮𝗰𝗰𝗲𝗽𝘁𝗧𝗿𝗮𝘃𝗲𝗹', 𝗹𝗮𝗯𝗲𝗹: '𝗔𝗰𝗰𝗲𝗽𝘁 ``𝗧𝗿𝗮𝘃𝗲𝗹', 𝗮𝗰𝘁𝗶𝗼𝗻𝗚𝗿𝗼𝘂𝗽𝗜𝗱: '𝗺𝗲𝗻𝘂-𝗖𝗵𝗮𝗻𝗴𝗲𝗦𝘁𝗮𝘁𝘂𝘀' },`\
 `{ 𝘁𝘆𝗽𝗲: #𝗙𝗢𝗥_𝗔𝗖𝗧𝗜𝗢𝗡, 𝗱𝗮𝘁𝗮𝗔𝗰𝘁𝗶𝗼𝗻: '𝗿𝗲𝗷𝗲𝗰𝘁𝗧𝗿𝗮𝘃𝗲𝗹', 𝗹𝗮𝗯𝗲𝗹: '𝗥𝗲𝗷𝗲𝗰𝘁 ``𝗧𝗿𝗮𝘃𝗲𝗹', 𝗮𝗰𝘁𝗶𝗼𝗻𝗚𝗿𝗼𝘂𝗽𝗜𝗱: '𝗺𝗲𝗻𝘂-𝗖𝗵𝗮𝗻𝗴𝗲𝗦𝘁𝗮𝘁𝘂𝘀' }`\
 `],`\
 `𝗶𝗱𝗲𝗻𝘁𝗶𝗳𝗶𝗰𝗮𝘁𝗶𝗼𝗻: [`\
 `{ 𝗽𝗼𝘀𝗶𝘁𝗶𝗼𝗻: 𝟭𝟬, 𝗹𝗮𝗯𝗲𝗹: '𝗧𝗿𝗮𝘃𝗲𝗹 𝗜𝗗' },`\
 `{ 𝘁𝘆𝗽𝗲: #𝗙𝗢𝗥_𝗔𝗖𝗧𝗜𝗢𝗡_𝗚𝗥𝗢𝗨𝗣, 𝗹𝗮𝗯𝗲𝗹: '𝗖𝗵𝗮𝗻𝗴𝗲 𝗦𝘁𝗮𝘁𝘂𝘀', 𝗮𝗰𝘁𝗶𝗼𝗻𝗚𝗿𝗼𝘂𝗽𝗜𝗱: ``'𝗺𝗲𝗻𝘂-𝗖𝗵𝗮𝗻𝗴𝗲𝗦𝘁𝗮𝘁𝘂𝘀' },`\
 `{ 𝘁𝘆𝗽𝗲: #𝗙𝗢𝗥_𝗔𝗖𝗧𝗜𝗢𝗡, 𝗱𝗮𝘁𝗮𝗔𝗰𝘁𝗶𝗼𝗻: '𝗮𝗰𝗰𝗲𝗽𝘁𝗧𝗿𝗮𝘃𝗲𝗹', 𝗹𝗮𝗯𝗲𝗹: '𝗔𝗰𝗰𝗲𝗽𝘁 ``𝗧𝗿𝗮𝘃𝗲𝗹', 𝗮𝗰𝘁𝗶𝗼𝗻𝗚𝗿𝗼𝘂𝗽𝗜𝗱: '𝗺𝗲𝗻𝘂-𝗖𝗵𝗮𝗻𝗴𝗲𝗦𝘁𝗮𝘁𝘂𝘀' },`\
 `{ 𝘁𝘆𝗽𝗲: #𝗙𝗢𝗥_𝗔𝗖𝗧𝗜𝗢𝗡, 𝗱𝗮𝘁𝗮𝗔𝗰𝘁𝗶𝗼𝗻: '𝗿𝗲𝗷𝗲𝗰𝘁𝗧𝗿𝗮𝘃𝗲𝗹', 𝗹𝗮𝗯𝗲𝗹: '𝗥𝗲𝗷𝗲𝗰𝘁 ``𝗧𝗿𝗮𝘃𝗲𝗹', 𝗮𝗰𝘁𝗶𝗼𝗻𝗚𝗿𝗼𝘂𝗽𝗜𝗱: '𝗺𝗲𝗻𝘂-𝗖𝗵𝗮𝗻𝗴𝗲𝗦𝘁𝗮𝘁𝘂𝘀' }`\
 `]`\
`}`

Note: I tried this in SAP BTP, ABAP Environment, and it works great! I believe this should also be available in SAP S/4HANA Public Cloud Latest version. For Private Cloud availability, I’ll check and share more once I have the details.

![Grouping_Actions_as_Menu_Button_using_Annotations ](/assets/images/Grouping_Actions_as_Menu_Button_using_Annotations.mp4)