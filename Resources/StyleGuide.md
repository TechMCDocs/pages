---
title: Style Guide
description: How to write the Wiki
---

# Style Guide

This article aims to provide a comprehensive style guide for all Technical Minecraft Wiki articles to follow. There are often disputes over which style rule or formatting to use so an official style guide helps resolve these disputes and reach a consensus. 

The style of this Wiki should follow Wikipedia's [Manual of Style](https://en.wikipedia.org/wiki/Wikipedia:Manual_of_Style). Wikipedia already provides a general style guide, but additional guidelines are needed for articles revolving around Minecraft. As such, only guidelines pertaining to the Technical Minecraft Wiki and its basic formatting rules are included here. If any contradiction arises, this page always has precedence over the Wikipedia style guide.

## Criteria for a Article
 - Articles must contain enough information to warrant a full page. If they do not have enough content, they should be merged with other similar articles.
 - Articles should pertain to Technical Minecraft in some way. 
 - Articles should not be about people or communities.
 
## Version Specific Content
Version specific information should be marked with the correct version range. This allows users to filter by Minecraft version. For example:
```html
<mc-version-block range=">=1.13">
This is a 1.13+ only paragraph. 
</mc-version-block>

This is a paragraph<mc-version range=">=1.13"> with some inline 1.13+ only text</mc-version>.
```
renders as:

<mc-version-block range=">=1.13">
This is a 1.13+ only paragraph. 
</mc-version-block>

This is a paragraph<mc-version range=">=1.13"> with some inline 1.13+ only text</mc-version>.

The `range` attribute is required and accepts a version range expression; text within the element is only enabled if the range matches the version that the user currently has selected. The operators `&&` (and), `||` (or), `!` (not) and `()` parentheses are supported.

If a version is in the format *A*.*b* (e.g. 1.12), then it is equivalent to *A*.*b*.x (e.g. 1.12.x) which matches any version between the first *A*.*b* snapshot and the last minor release of that version. If the version is in the format *A*.*b*.*c* (e.g. 1.12.0), then it matches only that minor version and its snapshots. If the version is in any other format (e.g. 22w11a), then it matches only that exact version. Comparison operators `=`, `>`, `>=`, `<` and `<=` are accepted; if no comparison operator is present then `=` is implied. Of course, this can be combined with `!` to produce ranges like `!=1.12`.

Some examples:
- `>=1.13`: 1.13 snapshots and later.
- `<1.13`: Anything strictly before 1.13 snapshots (i.e. 1.12.2 and below).
- `>=1.14.0 && <=1.14.2`: Between 1.14.0 (and its snapshots) and 1.14.2 inclusive.
- `>=1.8 && !(>=1.9 && <=1.10)`: Anything since 1.8 (and its snapshots) except between 1.9 (and its snapshots) and 1.10.

## Keeping Articles Concise
Here is an example a poor article with corrections. It uses a previous version of the Log article. Highlighted in <span style="background-color: rgba(252, 211, 77, 0.5)">yellow</span> is the redundant information, and in <span style="background-color: rgba(249, 168, 212, 0.5)">pink</span> the historical information. These parts should not be included in an article.

<div class="border py-2 px-5">
   <p><b>Wood</b> <span style="background-color: rgba(249, 168, 212, 0.5)">(previously known as <b>log</b>)</span> is a type of block <span style="background-color: rgba(249, 168, 212, 0.5)">first seen in <i>Minecraft</i> 0.0.14a</span>. They have a skin resembling bark on the four side faces, and a crosscut face on top and bottom.
      <span style="background-color: rgba(249, 168, 212, 0.5)">Only the normal oak logs are available in chunks generated before the Beta 1.2 update and all previous versions, while pine and birch generate in newer chunks</span>. 
        Wood is greatly abundant in naturally-generated maps, as it is used as the foundation for trees. Wood can be chopped by hand, but using an axe is faster. Wood is also flammable.
   </p>
   <p>Of the <span style="background-color: rgba(252, 211, 77, 0.5)">current</span> wood types, birch is the rarest type. <span style="background-color: rgba(252, 211, 77, 0.5)">They are often used to make plants, trees and wooden cabins.</span> <span style="background-color: rgba(249, 168, 212, 0.5)">In Survival Test, wood blocks drop 3–5 wooden planks when mined. In Indev, Infdev, Alpha, and Beta, mining a wood block drops a wood block instead</span>. <span>This allows the use of wood as a building material and is craftable into planks.</span></p>
   <p><span style="background-color: rgba(252, 211, 77, 0.5)">Wood's only crafting use is to be made into four wooden planks</span>. In addition, wood can be burnt in a furnace to make charcoal as a substitute for coal.</p>
   <p><span style="background-color: rgba(249, 168, 212, 0.5)">As of the Minecraft Beta 1.2 update on January 13, 2011, there are now four kinds of wood.</span><span style="background-color: rgba(252, 211, 77, 0.5)">One is the normal wood (oak), another resembles the wood of silver birch trees, yet another type resembles the normal wood, but it is darker and appears in pine/conifer trees that grow in colder biomes, the fourth type is similar to the oak wood, however there are some color differences, and it is tilted to one side.</span> Wood blocks produce 4 wooden planks when crafted. Wood from different types of trees do not stack in the inventory. <span style="background-color: rgba(249, 168, 212, 0.5)">Planks made from different kinds of trees used to be completely identical</span>. Birch trees have slightly duller colored leaves than regular trees, pine trees have pine needles, and jungle leaves are leafy with fruit looking shapes on them.</p>
   The fourth type of wood <span style="background-color: rgba(249, 168, 212, 0.5)">was introduced in snapshot 12w03a</span>, solely occurring in jungle biomes, and comprising trees exclusive to them. The tallest trees have this type of wood in 2x2 dimensions instead of the normal 1x1.
</div>
<br>

## Capitalization
If there is only one of a thing in intended to be in a world then it is a proper noun and should be capitalized. For example: Underground, there are randomly generated _mineshafts_. A _desert pyramid_ contains some rare loot. _Blazes_ spawn in _nether fortresses_; a _Nether_ structure. In _deep ocean_ biomes, _monuments_ can generate. A _stronghold_ is home to an _end portal_ that takes you to the _End_.

## Images
- Screenshots that use custom texture packs, UI mods and other custom content should not be used.
- Only one image should be used for each individual attribute of the article.
- Images should be dedicated to one attribute; showing parts of bigger contraptions should be avoided.
- Images should not have the sole purpose of showcasing a bug, instead link to the bug on the [official bug tracker](https://bugs.mojang.com/secure/Dashboard.jspa).

## Coordinates
- Single in-game coordinates should be capitalized and unspaced ("Y=1" instead of "y=1" or "y = 1").
- Coordinates should be in the order X, Y, Z with commas delimiting the individual coordinates.
- Volumes should be in the order X, Y, Z, with each item separated by a multiplication sign ("×"; &times;); "4×3×2" is an area that is 4 blocks wide along the X axis, 3 along the Y axis (vertical), and 2 along the Z axis.

## Commands 
- Commands should be in code blocks made with backticks <code>`</code>.
- Literal keywords that must be typed in chat do not have any brackets for formatting applied (e.g., `/data merge`).
- A list of valid keywords should be placed in parentheses with each option separated by a pipe (e.g., `(get|merge|set|remove)`.
- Variables must be inside angle brackets (e.g., `<target>`).
- Optional content must be inside square brackets, but these brackets should not replace any angle brackets (e.g., `[<scale>]` is an optional variable whereas `[scale]` is an optional keyword)

## Sources
- Information does not require sources if it can directly be seen in-game or are otherwise obvious.
- Other information that is not widely known, must be sourced with a proper reference.
- Do _not_ add content to an article without a proper source unless that content is obvious.

## Apply for Editor Role
You can [apply](https://forms.gle/3en2GGpVGbJobF8u9) to be an editor of the Wiki. Notify Earthcomputer on Discord once you have filled the form in. Your application will be accepted based on your trustworthiness.

The Wiki Editor role will grant you direct write access to the [pages repository](https://github.com/TechMCDocs/pages). This allows you to merge pull requests. Please adhere to the following rules:
- I may directly modify articles for minor edits only. A "minor edit" could be correcting spelling and grammar errors, adding a link, and similar. It does not include making changes to the meaning of the content. Anything more than a minor edit requires a pull request.
- I may merge other people's pull requests if I determine that it meets the Wiki's standards. I may *not* merge my own pull requests.
