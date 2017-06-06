
# WikiD

## Overview

"WikiD" is a python package to access Wikipedia locally and get information useful for Natural Language Processing in a way not possible with other wikipedia packages.

Wikipedia is accessible locally (as opposed to a web interface) primarily for speed reasons. I wanted the API to iterate through 100,000 pages in less than a minute for instance. Having Wikipedia locally also allows me to enrich it with information not stored in wikipedia pages (such as 'reverse links', i.e. Wikipedia links that point to the page rather than links that are in the page).

The wikid packages has a compile module which takes the raw wikipedia download (the entire xml file) and compiles it into a file structure designed for efficient access. Wikipedia compilation takes about 7 hours but it needs to be done only once per wikipedia download.

The runtime module is the most important one, it allows to allow pages in a variety of ways as described below.



```python
from wikid import *
```

## Loading

Loads prepared pickles (~1.2 Gb) into a "WikiD" object, so it can read the Wikipeida source xml files. Wikipedia itself is not loaded into memory (that would be way too large) but a number of indexes are.


```python
wkd = WikiD()
```

    LOADING TITLE TO INDEX
    LOADING INDEX TO TITLE


## Page:

"get_page" returns an "Page" Object that contains information about a Wikipedia page, sourced from a locally storred xml file.

Pages store attributes includes:
* Title
* The Raw Wikiepdia source xml
* Wikipedia Links in the page
* Wikipedia Pages pointing to the current page.*
* Whether or not its a person

(*Note: As of the current build, some links are missing)


```python
page_sj = wkd.get_page("Steve Jobs")
print(page_sj.title)
print(page_sj.links()[:50])
```

    Steve Jobs
    ['Apple.com', 'Jerry Brown', 'Dow Jones and Company', 'Dock (Mac OS X)', 'Apple Store', 'Chrisann Brennan', 'Jackling House', 'Pancreaticoduodenectomy', 'Microsoft', 'Transistor–transistor logic', 'Backdating', 'Apple Macintosh', 'Dementia', 'William Shakespeare', 'Sun Microsystems', 'Dieter Rams', 'West Coast Computer Faire', 'IBM Personal Computer', 'IWoz', 'Pong', 'Reality distortion field', 'Computer platform', 'Glass ceiling', 'NeXT Introduction', 'Market capitalization', 'WALL-E', 'Haidakhan Babaji', 'Relapse', 'Regis McKenna', 'Audiobook', 'Howard Vollum Award', 'Cupertino_ California', 'Dylan Thomas', 'Toy Story 3', 'Zen', 'Bill Hewlett', 'Classic Mac OS', 'Palo Alto_ California', 'San Jose_ California', 'Wayne Gretzky', 'Sōtō', 'ITunes', 'PC Magazine', 'Pixar', 'Simon and Schuster', 'Circuit board', 'Macworld Conference & Expo#2007', 'Commodore 64', 'United States Coast Guard', 'DNA paternity testing']


## Bidirectional links

Because we have all page links, we can actually identify which of the links point to pages that are pointing back to the current page. These 'bidirectional_links' are a subset of the 'links' and are often more interesting. They allow to prune out links that point to too generic pages. For instance, if a page about 'Bill Gates' points to 'Technology', the 'Technology' page is very generic but it is unlikely to point back to 'Bill Gates'. Hence, in that instance, 'Technology' will be a link but not a bidirectional link.


```python
page_bg = wkd.get_page("Bill Gates")

# is_person indicates whether the page describes a person
print(page_bg.is_person)

# bidirectional_links are links to pages that are pointing back at this page.
print(page_bg.bidirectional_links()[:50])

```

    True
    ['BgC3', 'Lakeside School (Seattle_ Washington)', 'World Economic Forum', 'Bono', 'Reddit', 'Microsoft', 'Criticism of Microsoft', 'Altair 8800', 'Omni Processor', 'Anthony Michael Hall', "The World's Billionaires", 'David Boies', 'The Giving Pledge', 'Micro Instrumentation and Telemetry Systems', 'Leonardo da Vinci', 'MITS Altair 8800', 'Carlos Slim', "Bill Gates' house", 'Jefferson Awards for Public Service', 'Melinda Gates', 'DFBCS', 'Nerds 2.0.1', 'TerraPower', 'National Merit Scholarship Program', 'The Dating Game', 'BASIC', 'William H. Gates Sr.', 'Berkshire Hathaway', 'Superintelligence: Paths_ Dangers_ Strategies', 'Christos Papadimitriou', 'Lists of billionaires', 'New York Institute of Technology', 'Harvard College', 'The Road Ahead (Bill Gates book)', 'The Power of Half', 'Mark Zuckerberg', 'PC DOS', 'Washington (state)', 'Corbis', 'Steve Ballmer', 'Forbes 400', 'OS/2', 'ResearchGate', 'John D. Rockefeller', 'Giving Pledge', 'PDP-10', 'Pancake sorting', 'Harry R. Lewis', 'British honours system', 'Teletype Model 33']


## Iteration through sections Wikipedia

For testing purposes, it is often useful to iterate through a subset of wikipedia. The example below shows how to get 10 consecutive pages at position 100,000.


```python
for page in wkd.range(2000,2010):
    if len(page.links()) > 10:
        print(page.title)
    
```

    Bursa
    The Bahamas
    Baker Island
    Bangladesh
    Barbados
    Bassas da India
    Belarus
    Belize
    Benin
    Bermuda



```python
bridge = wkd.get_page("Oberbaum Bridge")
print(bridge.reverse_links())
print(bridge.raw)
```

    ['List of sights in Berlin', 'Portal:Berlin/Topics', 'Culture in Berlin', 'Spree', 'Architecture in Berlin', 'Cengaver Katrancı', 'I Will Follow You', 'Timeline of Berlin', 'Template:Visitor attractions in Berlin', 'Template:Bridges of Berlin', 'Berlin', 'West Berlin', 'Friedrichshain-Kreuzberg', 'Fernsehturm Berlin', 'Berlin U-Bahn', 'Friedrichshain', 'Unknown (2011 film)', 'Template:Berlin Wall', '59th (2nd North Midland) Division']
      <page>
        <title>Oberbaum Bridge</title>
        <ns>0</ns>
        <id>2939238</id>
        <revision>
          <id>736276111</id>
          <parentid>735447604</parentid>
          <timestamp>2016-08-26T10:52:33Z</timestamp>
          <contributor>
            <username>Brewer Bob</username>
            <id>20461221</id>
          </contributor>
          <minor />
          <model>wikitext</model>
          <format>text/x-wiki</format>
          <text xml:space="preserve">{{unreferenced|date=April 2016}}
    [[Image:Oberbaumbrücke mit U-Bahn.jpg|thumb|An [[Berlin U-Bahn|U-Bahn]] train crosses the Oberbaum Bridge]]
    [[File:Oberbaumbruecke beim Berliner Osthafen cropped.jpg|thumb|Oberbaum Bridge connecting the districts of [[Kreuzberg]] and [[Friedrichshain]], [[Berlin TV Tower]] in the background]]
    
    The '''Oberbaum Bridge''' ({{lang-de|Oberbaumbrücke}}) is a double-deck bridge crossing [[Berlin]]'s [[River Spree]], considered one of the city's [[landmark]]s. It links [[Friedrichshain]] and [[Kreuzberg]], former [[Boroughs of Berlin|boroughs]] that were divided by the [[Berlin Wall]], and has become an important symbol of Berlin’s unity.
    
    The lower deck of the bridge carries a roadway, which connects [[Oberbaum Straße]] to the south of the river with [[Warschauer Straße]] to the north. The upper deck of the bridge carries [[Berlin U-Bahn|Berlin U-Bahn line]] {{BVG lines|U1}}, between [[Schlesisches Tor (Berlin U-Bahn)|Schlesisches Tor]] and [[Berlin Warschauer Straße station|Warschauer Straße]] stations.
    
    The bridge appears prominently in the 1998 film ''[[Run Lola Run]]''.
    
    ==History==
    [[Image:Oberbaumbrücke, Berlin 1900.png|thumb|left|The Oberbaum Bridge and former [[Stralauer Tor (Berlin U-Bahn)|U-Bahn railway station Stralauer Tor]], c. 1900]]
    [[Image:Prenzlau Mitteltor Turm 2006.jpg|thumb|right|The towers were based on the [[Brick Gothic]] ''Mitteltorturm'' in [[Prenzlau]]]]
    [[File:Oberbaum Bridge - december-31-2014.ogv|thumb|(video) A U-bahn subway train goes across the bridge on a cold day in December, 2014]]
    The bridge is built on the former boundary of the municipal area with its rural environs, where an [[Berlin Customs Wall|excise wall]] was built in 1732. A wooden drawbridge was built as part of the wall; it served as a gate to the city. The name ''Oberbaumbrücke'' stemmed from the heavy tree trunk, covered in metal spikes, that was used as a boom to block the river at night to prevent smuggling. (''Baum'' means tree or wooden beam in German; thus the name means something like &quot;Upper [Upstream] Tree Bridge&quot;; there was another tree-trunk barrier at the western end of the contemporary city limits, close to today's Unterbaumstraße (lit. in {{lang-en|Lower [Downstream] Tree Street}}.)
    
    By 1879 the wooden bridge had been modified greatly. At 154 meters it was Berlin's longest, but was no longer adequate to the amount of traffic crossing it. Plans began to be drawn up for a new stone construction. The [[Siemens &amp; Halske AG|Siemens &amp; Halske]] company, which was planning to build the [[Berlin U-Bahn]] ([[Rapid transit|subway]]), insisted on a combined crossing for road vehicles, pedestrians, and the new rail line.
    The new bridge opened in 1896 after two years of construction, in time for the [[Gewerbe-Ausstellung (1896)|Berlin Trades Exhibition]]. The architect and government official [[Otto Stahn]] (1859-1930) designed it in North German [[Brick Gothic#19th century Neogothic|Brick Gothic]], in the style of a city gate with many decorative elements, such as pointed arches, cross vaults, and coats of arms. The two towers were inspired by the Middle Gate Tower (''Mitteltorturm'') in the northern [[Brandenburg]] city of [[Prenzlau]]. Although purely cosmetic, they served as a reminder that the site was once Berlin’s river gateway.
    
    In 1902 the first segment of the U-Bahn opened. Its inaugural journey, carrying 19 passengers, ran from [[Stralauer Thor]], at the eastern end of the bridge, to Potsdamer Platz. Stralauer Thor was dismantled after being damaged in a 1945 air raid, but its four sandstone-clad support posts can still be seen.
    
    After Berlin absorbed several other municipalities in 1920, the Oberbaum Bridge became the crossing between the new boroughs of [[Friedrichshain]] and [[Kreuzberg]]. In April 1945 the [[Wehrmacht]] blew up the middle section of the bridge in an attempt to stop the [[Red Army]] from crossing it. After the war ended, Berlin was divided into four sectors. The Oberbaum Bridge crossed between the American and Soviet sectors. Until the mid-1950s, pedestrians, motor vehicles, and the city [[tram]]way were able to cross the bridge without difficulty.
    
    ===Border crossing===
    [[Image:Bundesarchiv Bild 183-85426-0002, Berlin, Mauerbau, Oberbaumbrücke.jpg|thumb|300px|East German checkpoint at the Oberbaum Bridge.]]
    [[File:Oberbaumbrücke 19891111.jpg|thumb|300px|Crowds at Oberbaumbrücke after the breach of the [[Berlin Wall]] in November 1989.]]
    
    When the Berlin Wall was built in 1961 the bridge became part of [[East Berlin]]'s border with [[West Berlin]]; as all the waters of the [[River Spree]] were in Friedrichshain, the [[East Germany|East German]] fortifications extended to the shoreline on the Kreuzberg side. The West [[Berlin U-Bahn]] line was forced to terminate at [[Schlesisches Tor (Berlin U-Bahn)|Schlesisches Tor]]. Beginning on 21 December 1963, the Oberbaum Bridge was used as a pedestrian border crossing for West Berlin residents only.
    
    After the removal of the [[Berlin Wall]] in 1989, and [[German reunification]] the following year, the bridge was restored to its former appearance, albeit with a new steel middle section designed by the Spanish architect [[Santiago Calatrava]]. It opened to pedestrians and traffic on 9 November 1994, the fifth anniversary of the opening of the Berlin Wall. The U-Bahn line to [[Berlin Warschauer Straße station|Warschauer Straße station]] was reopened a year later.
    &lt;!-- Deleted image removed: [[File:Oberbaum bridge.jpg|thumb|right|250px|Middle section redesigned by Calatrava showing the Rock,Paper/Scissors indicators]] --&gt;
    Since 1997, a [[neon lamp|neon]] [[Installation art|installation]] entitled &quot;Stone - Paper - Scissors&quot; by [[Thorsten Goldberg]] has adorned the bridge. Its two elements are engaged in a constant game of [[rock, paper, scissors]], suggesting the arbitrariness of immigration decisions, both during the [[Cold War]] and for today's [[asylum seekers]] and [[poverty migrants]].
    
    Since 1999, the traditional rivalry between the traditionally left-leaning boroughs [[Kreuzberg]] and [[Friedrichshain]] is played out in the annual &quot;water battle&quot;, where residents from both areas, organized in groups with satirical names such as &quot;[[Anarchism|Anarcho]]-[[Cynicism (contemporary)|Cynical]] Offensive Berlin - Friedrichshain Faction&quot; or &quot;Kreuzberg [[Landwehr]]&quot; pelt each other with rotten vegetables, jello, eggs, flour and water and try to symbolically &quot;reconquer&quot; the &quot;renegade&quot; other borough (Friedrichshain being mockingly referred to as &quot;East Kreuzberg&quot; and Kreuzberg as &quot;Lower Friedrichshain&quot;) by driving their participants from the bridge. Due to their higher turnout and the superior &quot;armament&quot; (including home-built [[water cannon]]s), the Friedrichshain detachments have won the water battle on a regular basis.
    
    With the creation of the unified [[Friedrichshain-Kreuzberg]] borough in 2001, the Oberbaum Bridge no longer crosses a jurisdictional boundary.
    
    == See also ==
    * [[:Category:Bridges in Berlin|Bridges in Berlin]]
    * [[List of road-rail bridges]]
    
    == References ==
    {{reflist}}
    
    == External links ==
    {{Commons|Oberbaumbrücke}}
    * [http://www.stadtentwicklung.berlin.de/bauen/wanderungen/en/s4_oberbaumbruecke.shtml Berlin Senate Department of Urban Development article]
    * [http://www.stadtentwicklung.berlin.de/bauen/ueberbruecken/en/text_19.shtml More information from above source]
    
    {{Berlin Wall}}
    {{Visitor attractions in Berlin}}
    {{coord|52|30|07|N|13|26|44|E|region:DE-BE_type:landmark|display=title}}
    
    [[Category:History of Berlin]]
    [[Category:Road-rail bridges]]
    [[Category:Bridges completed in 1896]]
    [[Category:Berlin border crossings]]
    [[Category:Bridges in Berlin]]
    [[Category:Buildings and structures in Friedrichshain-Kreuzberg]]
    [[Category:Road bridges in Germany]]
    [[Category:Railway bridges in Germany]]
    [[Category:Articles containing video clips]]</text>
          <sha1>nwpisvoxppvxysm3jepqz5y9wdfd459</sha1>
        </revision>
      </page>
    



```python
 
```
