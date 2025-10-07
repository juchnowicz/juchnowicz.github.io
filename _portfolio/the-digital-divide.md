---
title: "The Digital Divide"
excerpt: "Analysis of global internet penetration (2000–2023) via an interactive dashboard, revealing access gaps and proposing solutions to bridge the digital divide."
order: 3
header:
  image: /assets/images/internet-usage-dashboard-analysis-header.png
  teaser: /assets/images/internet-usage-dashboard-analysis.png
toc: true
toc_sticky: true
mathjax: true
---
**Notice 1:** 
Readers may find it helpful to keep the interactive dashboard open in a separate tab while going through the report. The dashboard can be viewed on the Tableau Public portal under [this hyperlink](https://public.tableau.com/views/GlobalInternetPenetrationandDigitalDivide2000-2023/BridgingtheDigitalDivide?:language=en-US&:sid=&:redirect=auth&:display_count=n&:origin=viz_share_link){:target="_blank"}.
{: style="text-align: justify;"}
{: .notice--primary}
{:#Notice1}
**Notice 2:** 
The source data, step-by-step data preparation and analysis in Python can be found in the Jupiter Notebook inside [this github repo](https://github.com/juchnowicz/internet-usage-dashboard-analysis.git){:target="_blank"}.
{: style="text-align: justify;"}
{: .notice--primary}
{:#Notice2}

## Executive Summary

The following analysis is built around an interactive dashboard that visualizes internet penetration trends across the world from 2000 to 2023 using data from the International Telecommunication Union (ITU) and complementary socio-economic and infrastructure indicators. The goal of the dashboard is to uncover global connectivity patterns, highlight disparities and identify factors influencing internet access at a country level. This report presents the dashboard’s findings and suggests solutions to narrow down the digital gap problem.
{: style="text-align: justify;"}

Here are the key findings from the dashboard analysis:
{: style="text-align: justify;"}
* **Global Progress:** Internet access has increased from just 10% of the global population in 2000 to nearly 70% by 2023, driven by mobile broadband, infrastructure investment, and digital innovation.
* **Persistent Digital Divide:** Despite overall growth, significant gaps remain. In 2023, penetration rates averaged 92% in developed countries but only 59% in developing nations. Africa and parts of Asia continue to lag due to economic, regulatory, and geographic challenges.
* **Success Stories:** Countries like Australia, Estonia, Brazil, and Senegal demonstrated rapid progress due to supportive policies, urbanization, and targeted investments in infrastructure and digital literacy.
* **High-Impact Factors:** Strong correlations were found between internet penetration and variables such as GDP PPP per capita, urbanization, regulatory strength, and education levels.
{: style="text-align: justify;"}

The following recommendations are intended to bridge the digital divide:
{: style="text-align: justify;"}
* **Infrastructure Investment:** Expand mobile and broadband networks in underserved areas using context-appropriate technology (e.g., mobile-first solutions in rural or mountainous regions).
* **Digital Literacy Programs:** Promote inclusive digital education to ensure access translates into effective use.
* **Policy and Regulatory Reform:** Support competition, public-private partnerships, and regulatory modernization to attract telecom investment.
* **Stability and Equal Access:** In politically unstable regions, international cooperation and rights advocacy are essential for equitable connectivity.
While the analysis yields important insights, it is limited by data gaps, self-reported metrics, and correlation-based methods. Future improvements include integrating more granular policy indicators, using machine learning models for scenario simulation, and forecasting future penetration trends to support long-term planning.
{: style="text-align: justify;"}

## Introduction
Access to the internet has become one of the defining factors of economic and social progress in the modern world. Over the past two decades, it has gone from a luxury to a necessity for everyone wanting to be included in the modern society. While global internet penetration has risen significantly, many countries still lag behind due to economic, infrastructural, and political barriers. Readers of this study might not realize that 2 out of 5 people around the world have never watched a YouTube video, can’t be reached via WhatsApp or FaceTime, and will miss out on the next trending Netflix series simply because they don’t have internet access. In my study, I explore and visualize how internet usage has evolved over time, highlight countries that are still significantly impacted by limited connectivity, and examine the relationships between internet penetration and socio-economic factors.
{: style="text-align: justify;"}


## Data Sources and Feature Selection
The core of this analysis is built on internet penetration data coming from the International Telecommunication Union (ITU) DataHub and contains the proportion of individuals who used the internet via any network by country by year.
{: style="text-align: justify;"}

**Notice:** It is necessary to clarify the wording used in this report. The term *internet penetration* refers to the proportion of individuals actively accessing the internet. Words synonymous with penetration used here are *access*, *usage*, *connectivity*, *online*, *availability*, *accessibility*. The remaining individuals are either outside of the internet coverage areas or within internet coverage but do not wish to use it. Words used to describe that group here include *offline* and *disconnected*.
{: style="text-align: justify;"}
{: .notice--info}

- `INTERNET_PENETRATION`[^1] - Proportion of individuals who used the internet in the last three months via any network.
- `COUNTRY_NAME`[^1] - Country name containing 228 unique countries.
- `REGION_NAME` - To enhance visualization clarity and allow for a broader level analysis, I grouped the data into regional clusters aligned with continents. one of the six regions: Africa, Asia, Europe, North America, Oceania, South America.
- `YEAR`[^1] - year between 2000 and 2023.
- `POPULATION_ONLINE` / `POPULATION_OFFLINE` - The number of people online or offline calculated by multiplying the penetration rate (`INTERNET_PENETRATION`) or non-penetration rate (`1 - INTERNET_PENETRATION`) respectively by total population (`POP_TOTAL`) for each country and year.
{: style="text-align: justify;"}

To understand the reasons for global disparities in internet access, I leveraged the research from the Institute of Electrical and Electronics Engineers (IEEE, 2023)[^2] to select various features from publicly available datasets and grouped them into four categories representing different challenges in increasing internet usage.
{: style="text-align: justify;"}

1. **Socio-Economic Constraints** – Developing countries often lack the financial resources to invest in necessary infrastructure, making internet access unaffordable for many citizens. Social disparities may also exclude some people from using the internet. The features I identified in this category were:
    - `GDP_PPP_PER_CAPITA`[^3] - Per capita gross domestic product (GDP) values expressed in current international dollars converted by purchasing power parity (PPP) conversion factor.
    - `HDI`[^4] - Human Development Index (HDI) which is a composite measure of a country’s development level ranging from 0 to 1 containing life expectancy, years of schooling and standard of living components. The higher the index, the higher the more developed the country.
    - `COST_PPP`[^5] - Fixed-broadband services price baskets in PPP terms. 
    {:#cost_ppp}
    - `URBAN_POP_PCT`[^6] - People living in urban areas as a percentage of total population.
    - `GII`[^7] - Global Inequality Index (GII) is a composite metric of gender inequality, ranging from 0, where women and men fare equally, to 1, where one gender fares as poorly as possible in all measured dimensions.
    - `POP_65_AND_OVER`[^8] - Percentage of population aged 65 and over.
    - `POP_TOTAL`[^9] - Total country population.
    - `LAND_AREA` [^10] - Total country land area in square kilometers.
    - `POPULATION_DENSITY` - Total country population divided by country land area.
2. **Inadequate Infrastructure** – Limited access to reliable electricity and telecommunications networks hampers the deployment of internet services.
    - `FIXED_SUBS`[^11] - Fixed subscriptions to high-speed access to the internet at downstream speeds equal to, or greater than, 256 kbit/s per 100 people.
    - `MOBILE_SUBS`[^12] - Sum of active handset-based and computer-based (USB/dongles) mobile-broadband subscriptions that allow access to the internet per 100 people.
3. **Educational Barriers** – A lack of digital literacy and educational opportunities prevents individuals from effectively utilizing digital technologies.
    - `POP_HIGH_EDU`[^13] - Percentage of population aged 25-34 with highest level of education completed.
    {:#pop_high_edu}
    - `HDI`[^4] - although already mentioned above, it also contains a years of schooling component as a proxy for education quality.
    - `DEVELOPMENT_STATUS` - Field containing one of two values: either *Developed* if the country’s HDI is at least 0.8 or *Developing* if a country’s HDI is less than 0.8.
4. **Policy and Regulatory Challenges** – lack of policies and regulatory frameworks can stifle investment and innovation in the Information and Communication Technology (ICT) sector.
    - `ICT_TRACKER`[^14] - ICT Regulatory Tracker score that ranges from 0 to 100. It measures the existence of regulatory indicators listed by the International Telecommunication Union. The higher the score, the more modern and advanced the regulatory framework.
{: style="text-align: justify;"}

[^1]: Source: [ITU DataHub: Individuals using the Internet](https://datahub.itu.int/data/?i=11624)

[^2]: Source: [Digital Divide in Developing Countries: Why We Need to Close the Gap](https://ctu.ieee.org/blog/2023/01/23/digital-divide-in-developing-countries-why-we-need-to-close-the-gap/)

[^3]: Source: [World Bank Group Data: GDP per capita, PPP (current international $)](https://data.worldbank.org/indicator/NY.GDP.PCAP.PP.CD)

[^4]: Source: [UNDP Human Development Reports: HDI value](https://hdr.undp.org/data-center/documentation-and-downloads)

[^5]: Source: [ITU ICT Price Basket: Fixed-broadband basket](https://www.itu.int/en/ITU-D/Statistics/Pages/ICTprices/default.aspx)

[^6]: Source: [World Bank Group Data: Urban population (% of total population)](https://data.worldbank.org/indicator/SP.URB.TOTL.IN.ZS)

[^7]: Source: [UNDP Human Development Reports: GII value](https://hdr.undp.org/data-center/documentation-and-downloads)

[^8]: Source: [World Bank Group Data: Population ages 65 and above (% of total population)](https://data.worldbank.org/indicator/SP.POP.65UP.TO.ZS)

[^9]: Source: [World Bank Group Data: Population, total](https://data.worldbank.org/indicator/SP.POP.TOTL)

[^10]: Source: [World Bank Group Data: Land area (sq. km)](https://data.worldbank.org/indicator/AG.LND.TOTL.K2)

[^11]: Source: [ITU DataHub: Fixed-broadband subscriptions](https://datahub.itu.int/data/?i=19303)

[^12]: Source: [ITU DataHub: Active mobile-broadband subscriptions](https://datahub.itu.int/data/?i=11632)

[^13]: Source: [OECD Data: Population with tertiary education](https://www.oecd.org/en/data/indicators/population-with-tertiary-education.html)

[^14]: Source: [ITU: ICT Regulatory Tracker](https://app.gen5.digital/tracker/metrics)

## Methodology
After transforming the datasets to a standardized format, I combined them with the core internet usage data and performed EDA, cleanup and feature engineering. Several fields were created for the purpose of visualizing the penetration gaps and the scale of disconnection from the digital world. These included `REGION_NAME`, `POPULATION_ONLINE` / `POPULATION_OFFLINE`, `POPULATION_DENSITY` and `DEVELOPMENT_STATUS`. In order to check the impact of each feature on internet access, I calculated the correlation coefficients against `INTERNET_PENETRATION`. The detailed steps are laid out in the notebook linked [at the top](#Notice2).
{: style="text-align: justify;"}

For the dashboard visualizations, I included only features strongly correlated with `INTERNET_PENETRATION` where the correlation coefficient is greater than 0.5 ( $$\lvert r \rvert >0.5$$ ). You can use the interactive scatterplot by changing the measures and playing the year animation to see the type of association between the variables and internet penetration. Despite the high correlation, the variable 'POP_65_AND_OVER' is not present in the visualizations due to many countries missing data.

{:#selected_measures}
|Variable|Corr. $$r$$|Description|
|----|----|----|
|`FIXED_SUBS`|0.81|There is a very strong positive correlation between fixed high-speed subscriptions and internet penetration. Initially the association appeared to be linear but as more countries converged towards near-full penetration from 2012 onwards, the relationship changed to logarithmic.|
|`HDI`|0.77|During the early 2000s the association between HDI and internet penetration was strongly exponential showcasing a big gap between developed and remaining countries but later on the association became increasingly linear.|
|`MOBILE_SUBS`|0.72|Similarly to fixed subscriptions, the relationship between mobile-broadband subscriptions and penetration changed from linear to logarithmic over the years. In this case, the correlation coefficient is smaller due to more outliers and lack of data in the early 2000s.|
|`POP_HIGH_EDU`|0.72|The association with internet penetration was predominantly linear until the late 2010s when it began to resemble a logarithmic curve as internet usage in developed countries approached 100% and data from developing countries became available. |
|`GDP_PPP_PER_CAPITA`|0.67|Noticeable linear relationship was visible for most of the years with several outliers being rich middle eastern countries that were late to increase internet usage among their citizens. Starting from 2015, the relationship resembled a logarithmic curve. |
|`URBAN_POP_PCT`|0.55|The relationship with internet penetration appears exponential at the beginning of the study and transforms into linear at the end. However, there are numerous outliers like smaller countries in Europe, Carribean, developed Asian countries and the USA. |
|`ICT_TRACKER`|0.52|The ICT data is available only from 2007 to 2022. During that period, a positive relationship with internet penetration can be observed but there are multiple outlier countries on both sides of the spectrum. Many countries in Africa have high ICT scores but low internet penetration and several nations with highly regulatory environments score low on the ICT Tracker but achieved high penetration. |
|`GII`|-0.74|The higher the gender inequality, the lower the internet penetration. That strong pattern applies to the entire study period, initially following an exponential decay curve and turning into a negative linear relationship towards the end. |
{: style="text-align: justify;"}

{% raw %}
<div class='tableauPlaceholder' id='viz1758369588421' style='position: relative'>
  <noscript>
    <a href='#'>
      <img 
        alt='Usage scatterplots - 2022'
        src='https://public.tableau.com/static/images/Tr/TrackingInternetAccessandDisconnectedRegions2000-2023usagescatterplot/Usagescatterplots/1_rss.png'
        style='border: none'
      />
    </a>
  </noscript>
  <object class='tableauViz' style='display:none;'>
    <param name='host_url' value='https%3A%2F%2Fpublic.tableau.com%2F' />
    <param name='embed_code_version' value='3' />
    <param name='site_root' value='' />
    <param name='name' value='TrackingInternetAccessandDisconnectedRegions2000-2023usagescatterplot/Usagescatterplots' />
    <param name='tabs' value='no' />
    <param name='toolbar' value='yes' />
    <param name='static_image' value='https://public.tableau.com/static/images/Tr/TrackingInternetAccessandDisconnectedRegions2000-2023usagescatterplot/Usagescatterplots/1.png' />
    <param name='animate_transition' value='yes' />
    <param name='display_static_image' value='yes' />
    <param name='display_spinner' value='yes' />
    <param name='display_overlay' value='yes' />
    <param name='display_count' value='yes' />
    <param name='language' value='en-US' />
  </object>
</div>

<script type='text/javascript'>
  var divElement = document.getElementById('viz1758369588421');
  var vizElement = divElement.getElementsByTagName('object')[0];
  vizElement.style.width = '100%';
  vizElement.style.height = (divElement.offsetWidth * 0.75) + 'px';
  
  var scriptElement = document.createElement('script');
  scriptElement.src = 'https://public.tableau.com/javascripts/api/viz_v1.js';
  vizElement.parentNode.insertBefore(scriptElement, vizElement);
</script>
{% endraw %}

## Visualizing Global Connectivity
This analysis is driven by an interactive Tableau dashboard that I developed as an integral component of this report. The dashboard is displayed below and [linked above](#Notice1) for reader's convenience. The conclusions presented here are supported by dashboard visualizations, providing deeper insights into the trends and disparities in global internet penetration.
{: style="text-align: justify;"}
{% raw %}
<div class='tableauPlaceholder' id='viz1758332678180' style='position: relative'>
  <noscript>
    <a href='#'>
      <img 
        alt=' ' 
        src='https://public.tableau.com/static/images/Gl/GlobalInternetPenetrationandDigitalDivide2000-2023blogoptimized/TheRoadtoFullyConnectedWorld/1_rss.png' 
        style='border: none' 
      />
    </a>
  </noscript>
  <object class='tableauViz' style='display:none;'>
    <param name='host_url' value='https%3A%2F%2Fpublic.tableau.com%2F' />
    <param name='embed_code_version' value='3' />
    <param name='site_root' value='' />
    <param name='name' value='GlobalInternetPenetrationandDigitalDivide2000-2023blogoptimized/TheRoadtoFullyConnectedWorld' />
    <param name='tabs' value='yes' />
    <param name='toolbar' value='yes' />
    <param name='static_image' value='https://public.tableau.com/static/images/Gl/GlobalInternetPenetrationandDigitalDivide2000-2023blogoptimized/TheRoadtoFullyConnectedWorld/1.png' />
    <param name='animate_transition' value='yes' />
    <param name='display_static_image' value='yes' />
    <param name='display_spinner' value='yes' />
    <param name='display_overlay' value='yes' />
    <param name='display_count' value='yes' />
    <param name='language' value='en-US' />
  </object>
</div>

<script type='text/javascript'>
  var divElement = document.getElementById('viz1758332678180');
  var vizElement = divElement.getElementsByTagName('object')[0];
  if (divElement.offsetWidth > 800) {
    vizElement.style.width = '650px';
    vizElement.style.height = '910px';
  } else if (divElement.offsetWidth > 500) {
    vizElement.style.width = '650px';
    vizElement.style.height = '910px';
  } else {
    vizElement.style.width = '100%';
    vizElement.style.height = '1900px';
  }
  var scriptElement = document.createElement('script');
  scriptElement.src = 'https://public.tableau.com/javascripts/api/viz_v1.js';
  vizElement.parentNode.insertBefore(scriptElement, vizElement);
</script>
{% endraw %}

### The Road to a Fully Connected World
The first set of visualizations presents how internet penetration changed since the year 2000 at the global level, by continent and country. In 2000 there were only 400 million people connected to the web in the entire world. At that time the NASDAQ peaked last time before the Dotcom bubble burst, Amazon started offering products other than books, Google was becoming popular as a search engine and services like Youtube, Facebook, Wikipedia or even Myspace didn’t exist yet. By 2019 there were more people online than offline and by 2023 over 5.3 billion people had access to the internet. During those 23 years the global population went from nearly 6 billion to over 8 billion people. 
This means that internet penetration has seen an impressive rise in the past two decades going from 10% to nearly 70%. This exponential growth is largely driven by infrastructure expansion, mobile broadband adoption, and declining costs of connectivity.
My time series analysis of internet penetration from 2000 to 2023 reveals a consistent upward trend, with noticeable periods of acceleration. The first one occured around 2006, coinciding with the emergence of social media like Facebook, YouTube, Instagram or Snapchat.[^15] The second acceleration took place around 2018, along with the peak of the smartphone era[^16] and the rise of mobile networks in developing regions.[^17] Based on the visualized data collected data and additional research, I identified three main drivers of the global increase in internet access.
{: style="text-align: justify;"}

1. **Technological Advancements** - the rollout of fiber-optic networks has significantly increased broadband speeds and reduced latency, enabling more reliable connections and opening doors to new service offerings. Additionally, the development of mobile (4G, 5G) and satellite technologies has made internet access widely available, even in remote regions. One of the major turning points was also the proliferation of smartphones and apps after the release of first iPhone in 2007 and first Android phone in 2008. 
2. **Government and Private Sector Initiatives** - Many countries have implemented programs aimed at reducing the digital divide by subsidizing infrastructure in rural and underserved areas. For instance, the US Federal Communications Commission (FCC) introduced the Connect America Fund, which provides funding to ISPs to expand broadband coverage in rural areas.[^18] Similarly, the European Union’s Digital Agenda has promoted competition among ISPs, driving down costs for consumers and increasing broadband penetration across member states.[^19] In emerging markets, proactive government policies have spurred competition and innovation. For example, Brazil’s National Broadband Plan focused on extending fiber-optic networks to smaller municipalities,[^20] while regulatory actions in countries like Indonesia have encouraged competition among telecom providers, leading to more affordable mobile data plans.[^21]
3. **Economic Mechanisms** - Increased demand for broadband services, fueled by network effects and economies of scale allowed companies to offer more affordable and better devices and services. For example, India’s telecom market saw a massive transformation with the launch of Reliance Jio in 2016, which provided limited-time free services, low-cost mobile data and affordable 4G smartphones. The network brought in over 100 million users in the first 6 months, disrupting the market. This impact on digital services, mobile phone adoption and rise in the internet usage in India is referred to as the „Jio Effect”.[^22] On the other hand, online platforms like Facebook, Instagram or TikTok have used network effects to their advantage, driving up demand for broadband at the same time. Streaming services such as Netflix or YouTube, have further increased demand for faster and more reliable internet connections. E-commerce and digital payment systems have also played a key role in internet adoption. In China, Alibaba and WeChat have revolutionized online shopping and mobile payments, making cashless transactions the norm. Similarly, in Latin America, platforms like MercadoLibre and Pix have enabled people to engage in digital commerce. The rise of fintech solutions like Unified Payments interface (UPI) in India and M-Pesa in Africa demonstrated how online financial services can drive internet adoption. UPI - backed by the government, banking institutions, and central bank of India - became so popular that it has largely replaced cash in urban areas. The interoperability, ease of use and no-fee model made it appealing to both consumers and merchants. Those who were initially hesitant to adopt it, eventually had to do so to stay competitive. Today, virtually every business, street vendor and rickshaw driver in major Indian cities accepts digital payments.[^23]
{: style="text-align: justify;"}

[^15]: Source: [The Evolution of Social Media: How Did It Begin, and Where Could It Go Next? May 28, 2020](https://online.maryville.edu/blog/evolution-social-media/)

[^16]: Source: [Have We Passed the Peak of the Smartphone Era? Feb 23, 2024](https://www.statista.com/chart/12798/global-smartphone-shipments/)

[^17]: Source: [GSMA Global mobile trends October 2016; Consumer insights – Mobile adoption and device ownership; Top 10 countries by projected new mobile subscribers, 2015–2020](https://www.gsmaintelligence.com/research/research-file-download?id=18809377&file=global-mobile-trends-1482139998965.pdf)

[^18]: Source: [FCC, Connect America Fund (CAF)](https://www.fcc.gov/general/connect-america-fund-caf)

[^19]: Source: [European Parliament, Fact Sheets of the European Union, Digital agenda for Europe](https://www.europarl.europa.eu/factsheets/en/sheet/64/digital-agenda-for-europe)

[^20]: Source: [Tech in Brazil, Brazil's Programa Nacional de Banda Larga](https://techinbrazil.com/brazil-s-programa-nacional-de-banda-larga)

[^21]: Source: [Global Compliance News, Indonesia: Omnibus Law – Chasing Efficiency in the Telecommunications Sector](https://www.globalcompliancenews.com/2020/11/19/indonesia-omnibus-law-chasing-efficiency-in-the-telecommunications-sector-19102020/)

[^22]: Source: [Quartz, Reliance Jio’s cheap data turned India’s internet dreams into reality](https://qz.com/india/2055771/reliance-jios-cheap-data-turned-indias-internet-dreams-into-reality)

[^23]: Source: [A. Copestake, D. Kriti, M.S. Martinez Peria (2025), Growing Retail Digital Payments: The Value of Interoperability](https://www.elibrary.imf.org/view/journals/063/2025/004/article-A001-en.xml)

#### The Digital Divide
The gap in internet access between different regions, referred to as *digital divide*, is a crucial issue because it exacerbates existing inequalities, particularly in underdeveloped countries. One of the most impacted areas is labor market where limited internet access equals restricted participation and job creation, stifling economic growth. According to IEEE (2023)[^24], over 80% of middle-skilled jobs now require digital skills, while those that don’t tend to offer lower wages, trapping individuals in cycles of low income and limited opportunity. The divide also deepens educational gaps, as students without reliable internet access miss out on online classes, widening the knowledge gap. Lastly, the lack of connectivity contributes to social isolation by cutting people off from digital social networks and essential services, diminishing overall quality of life.
{: style="text-align: justify;"}

#### Developed vs. Developing Nations
Despite global advancements in internet connectivity, not all regions and countries are increasing internet access at the same rate. A significant digital divide persists, specifically between developed and developing nations. The [chart below](#developing_developed_plot) shows a staggering difference in internet connectivity between those two groups over time. In 2000, internet penetration in developed countries stood at 34% while in developing countries, only 2% of people were connected to the web. 22 years later, developing countries still had not caught up with the pace of the developed nations, achieving penetration rates of 59% and 92% respectively. In fact, up until 2007 that digital gap grew bigger and bigger, reaching as much as 56 p.p., before it started narrowing down.
Looking at internet usage by region, it’s evident that Africa and Asia are lagging behind and consistently ranking below the world average. Those two continents also happen to be the most populated ones, meaning that nearly one third of the world’s population remains offline. On the other hand, Europe, North America and Oceania have always been leaders in connecting their citizens to the internet achieving usage rates of around 80% and above by 2023. Interestingly, South America has whitnessed the fastest-growing usage over the past two decades, advancing from single-digit access rates in early 2000s to 79% in 2022.
{: style="text-align: justify;"}
{:#developing_developed_plot}
{% raw %}
<div class='tableauPlaceholder' id='viz1758461752250' style='position: relative'>
  <noscript>
    <a href='#'>
      <img alt='Usage by Region '
           src='https://public.tableau.com/static/images/Tr/TrackingInternetAccessandDisconnectedRegions2000-2023developingvsdeveloped/UsagebyRegion/1_rss.png'
           style='border: none' />
    </a>
  </noscript>
  <object class='tableauViz' style='display:none;'>
    <param name='host_url' value='https%3A%2F%2Fpublic.tableau.com/' />
    <param name='embed_code_version' value='3' />
    <param name='site_root' value='' />
    <param name='name' value='TrackingInternetAccessandDisconnectedRegions2000-2023developingvsdeveloped/UsagebyRegion' />
    <param name='tabs' value='no' />
    <param name='toolbar' value='yes' />
    <param name='static_image'
           value='https://public.tableau.com/static/images/Tr/TrackingInternetAccessandDisconnectedRegions2000-2023developingvsdeveloped/UsagebyRegion/1.png' />
    <param name='animate_transition' value='yes' />
    <param name='display_static_image' value='yes' />
    <param name='display_spinner' value='yes' />
    <param name='display_overlay' value='yes' />
    <param name='display_count' value='yes' />
    <param name='language' value='en-US' />
  </object>
</div>

<script type='text/javascript'>
  var divElement = document.getElementById('viz1758461752250');
  var vizElement = divElement.getElementsByTagName('object')[0];
  vizElement.style.width = '100%';
  vizElement.style.height = (divElement.offsetWidth * 0.75) + 'px';
  
  var scriptElement = document.createElement('script');
  scriptElement.src = 'https://public.tableau.com/javascripts/api/viz_v1.js';
  vizElement.parentNode.insertBefore(scriptElement, vizElement);
</script>
{% endraw %}

### Bridging the gaps
The „Bridging The Gaps” dashboard combines connectivity indicators in the form of KPIs and interactive charts representing penetration by country and the relationship between penetration and [selected measures](#selected_measures) that are highly correlated with penetration. The dashboard can be filtered by region, year and a customizable usage threshold and it can be animated over time. The user is encouraged to identify the key reasons behind the divide and the countries most affected by low connectivity.
In this section I perform a review of low and high penetration regions, investigate the features affecting internet usage and put forward remedies to reduce the number of people offline and close the digital gap. I first pinpoint areas with the highest penetration and highest growth over time, look at their indicators and uncover their success stories. This overview is meant to set a benchmark and help with formulating an action plan for low performing countries since those nations might share a similar profile to the ones that have successfully expanded internet access despite facing comparable challenges. 
{: style="text-align: justify;"}


In the two most populous developing countries in the world - India and China - the number of mobile broadband subscriptions per 100 inhabitants went from 1 and 9 respectively in 2011 to 60 and 128 in 2023.

#### High Internet Penetration Regions
Looking at the world map and how internet penetration shaped over time, there are a few regions that stand out in terms of high internet usage and fast growth rate. Beginning from early 2000s, **Australia** exhibited over 50% penetration rates, higher than most European countries at that time and on par with the US. Ever since then, the access rate has been steadily growing and stands at 97% as of 2023. This is due to high GDP PPP per capita (ranked 19th in 2023) that allows for investments in new technologies but also leads to high affordability and adoption of internet services as well as high digital literacy and education standards (ranked 11th in HDI as of 2022). Additionally, 87% of Australians live in urban areas where internet infrastructure is well-developed and easier to deploy. Australian government actively promotes digital connectivity through initiatives such as National Broadband Network (NBN) that designs, builds and operates a broadband wholesale access network in the country.[^25]
{: style="text-align: justify;"}
**North America** and specifically the United States have historically led the way in developing and building telecommunications technologies, including broadband, fiber-optic and mobile networks. For most of the 2000s the region was the leader in internet penetration which stood at 88% as of 2002 for the entire continent. High connectivity rates are the result of strong economies and high disposable incomes (US ranked 10 and Canada ranked 25 in GDP PPP per capital as of 2022). Over 80% people live in large metropolitan areas like New York, Toronto or Mexico City making services more accessible and affordable and broadband companies are investing nearly 100 billion USD in infrastructure alone.[^26] On top of that, governments support the enabling of digital transformation through market regulations or programs that aim to provide internet access to underserved areas like Rural Digital Opportunity Fund in the US or Universal Broadband Fund in Canada.
{: style="text-align: justify;"}
**Europe** as a continent is currently the region with the highest internet penetration but it wasn’t always the case. In early 2000 only 15% of Europe was connected to the internet vs 30% in North America. Compared to Australia and North America, Europe’s population is more scattered across the continent. While in some countries like UK and France over 80% of people live in urban areas, in countries like Poland or Romania around half of the people live outside of the cities which makes infrastructure deployment challenging. However, some European countries are among the richest (4 out of top 10 countries by GDP PPP per capita as of 2022) and most developed (8 out of top 10 countries by HDI as of 2022) in the world. From a legislative standpoint, the European Union (EU) enforces strict anti-monopoly regulations to maintain a competitive telecom market. Examples include Local Loop Unbundling (LLU) which requires incumbent operators to allow competitors access to their local network infrastructure lowering barriers to entry into the market[^27] or European Electronic Communications Code which promotes investments in high-capacity networks enhances consumer rights and fosters competition.[^28] In consequence, Europe has some of the highest numbers of fixed broadband subscribers, despite the geographical challenges. Other initiatives like the Digital Agenda for Europe 2010-2020[^19] successfully reduced prices for internet services, eliminated roaming charges in the EU and enhanced internet connectivity by promoting investments in mobile and satellite technologies. Some European countries have successfully transformed into digital societies like Estonia where almost every bureaucratic task (e.g. voting or marriage) can be done online.[^29]
{: style="text-align: justify;"}
Oil-rich countries in the **Middle East** have the highest internet penetration rates and some of the fastest developing networks in the world. This is due to high GDP coming from the vast oil and gas reserves and high disposable incomes of their citizens. Densely populated cities also contribute to the high broadband accessibility with more than 85% of people living in urban areas. The governments of those countries invest heavily in digital infrastructure with the aim to become technological leaders, independent of their oil revenue in the future. To put this into perspective, the government of Saudi Arabia, a country of 33 million people has invested $25 billion in infrastructure over the past five years and will invest another $20 billion in the next five years. On top of that the governments are investing in the development of smart cities like NEOM (a $500 billion project) that will include AI-driven infrastructure including fully automated transportation.[^30]
{: style="text-align: justify;"}
**South America**, more than any other continent, experienced the fastest growth in internet usage in the past two decade, specifically Brazil, Argentina, Chile and Colombia. This is despite their relatively low GDP and HDI numbers. Firstly, the population distribution favors network deployment with majority of people living in urban areas (88% in Brazil and Chile and 92% in Argentina). Secondly, biggest countries in South America launched government initiatives aimed at increasing internet access. For example Brazil’s National Broadband Plan (PNBL) was set up in 2010 to expand affordable broadband access, particularly in underserved regions. Brazil’s digital plan focused on expanding connectivity through a national fiber-optic backbone, including 25,000 km of new cables, radio towers and an undersea link to Europe. To make access more affordable, the government set minimum broadband standards, capped prices, and provided free public Wi-Fi. The plan also incentivized private investment in digital infrastructure by granting tax exemptions.[^20] By 2014, 55% of Brazilians had access to the internet with broadband subscriptions growing from 13 to 21 million in just 3 years and mobile broadband connectivity surpassing targets by over 100%. Soon after, other South American governments followed suit like Argentina with Plan Conectar[^31] and Chile with Fibra Optica Austral to expand fibre-optic infrastructure to remote and rural regions of Chile.[^32] Within South America there are multiple national organizations (e.g. Recode) or international groups (e.g. eLAC) that promote the development of the digital ecosystem, specifically in low-income, rural and indigenous communities.[^33] Notably, countries that had those programs in place were able to narrow down the internet usage gap between low-income and high-income households. By 2022 in Argentina and Chile, the difference in penetration rates between the first and last quintile of income distribution was only 5 and 4 percentage points respectively.[^34] Brazil and Chile are one of the most advanced countries in South America regarding policy and regulatory frameworks for the telecommunications sector according to the ICT Regulatory Tracker with scores of 94 and 89 respectively.
{: style="text-align: justify;"}

#### Lowest Internet Penetration Regions
Despite global improvements, some countries and regions continue to struggle with internet accessibility. Looking at the regions on the first dashboard, Africa and Asia have been consistently reporting the lowest usage rates over the past 20 years and the gap between Africa’s and global rates has only been widening. However, not all Asian and African countries follow that trend. For instance, in Morocco, Gulf Countries mentioned before, Malaysia or South Korea over 90% of population has access to the internet as of 2023. Therefore, I leveraged the „Bridging the Gap” dashboard to perform a more granular analysis at the country level by applying an arbitrary usage threshold and selecting the option to filter for countries below the threshold. There are 24 countries where less than 30% of the population had access to the internet in 2023. Most of them lie in northern and eastern sub-Saharan region. Four countries are in Asia (Yemen, Pakistan, Afghanistan and Turkmenistan) and two (Palau and Papua New Guinea), in Oceania. Out of the 2.7 billion people offline in 2023, 0.7 billion of them reside in the 24 countries with lowest usage. I looked at the correlated features for those countries to check whether they might be the reason for the low penetration or whether there were some additional factors in play.
{: style="text-align: justify;"}
Two countries on the list are **outliers** as they don’t follow the expected correlation patterns. First one is Turkmenistan - one of the most digitally-isolated societies in the world where internet is heavily regulated. According to CIA World Factbook[^35] the country’s government maintains strict control over internet access with all internet users required to identify themselves before logging on. Turkmenistan ranks among the lowest globally for press freedom with most social networks blocked and it is the second-lowest ranked country on the ICT Tracker. For those reasons, the country has one of the lowest internet penetration rates despite high HDI and GDP numbers. The second country is Palau which is an archipelago on the Pacific Ocean with a population of fewer than 20,000 people. In this case I imputed the missing penetration with most recent values during the cleanup. However, the latest penetration rate provided by ICT dates back to 2004 and there is no reliable data from recent years. World trends and economic indicators suggest that Palau’s current penetration rate is much higher but I did not find any reliable data sources to back up that claim. 
{: style="text-align: justify;"}
19 out of the 24 countries with weakest connectivity lie in **Africa**. Throughout the years, African nations have lagged behind in this area. The dashboard analysis suggests that multiple factors might be contributing to this persistent issue. Economically, those are the poorest countries in the world with GDP PPP per capita ranging from 920 to 4019 in 2023 which means that internet access is unaffordable for individuals and governments don’t have the funds to finance or subsidize big infrastructure projects. Literacy levels are low among those nations which is reflected in low HDI ranging from 0.38 to 0.55 in 2022 and gender inequality remains high with GII ranging from 0.48 to 0.67 in 2022 compared to the world average of 0.33. Geographical constraints make it additionally difficult to build infrastructure since in many African countries people live outside of urban areas. For example in countries like Ethiopia, Uganda and Niger, fewer than 30% of people live in urban areas which leaves mobile and satellite solutions as the only feasible options. Finally, several countries like Eritrea, Ethiopia or South Sudan rank low on the ICT Tracker, lacking regulations designed to encourage competition and investments in the digital sector. The majority of people in low-performing African nations rely on mobile services. In most of those countries, fixed broadband subscribers do not exceed 1 in 100 people. Garcia-Swartz and Campbell-Kelly (2022)[^36] provide more context into the reasons for low penetration in African states. They mention that several of them like Ethiopia, Somalia or Niger have experienced civil wars, political instabilities and security challenges that have disrupted infrastructure investments. Telecom expansion was hampered by bureaucratic inefficiencies and corruption, and in some cases, providers had to negotiate with rebel groups to install equipment. The ITU report about regulation in Democratic Republic of the Congo (DRC),[^37] outlines several factors contributing to low internet penetration in the DRC. While urban areas have reliable telecom networks, rural areas lack infrastructure, forcing reliance on expensive mobile or satellite data connections. Relatively high taxes and duties on telecom equipment further increase costs for consumers. Private sector investment is slow due to regulatory uncertainty, political instability, unclear licensing policies and lack of sufficient incentives for operators to expand service in remote areas. Furthermore, government program implementation, including Universal Service, is slow and funding coming from levies on private sector companies has not been effectively used. However, **a few African countries have overcome those challenges** and noticeably increased their penetration rates over the past 20 years. One of such examples is Senegal where the internet penetration rate increased from 13% in 2013 to 61% in 2023 despite the socio-economic conditions. In terms of HDI and GDP PPP per capita, Senegal ranks below the African average and on par with the 18 African countries with the lowest penetration. The urban population percentage as of 2023 (50%) is slightly higher than the continental average (48%) but similar to other countries with much lower internet penetration rates like Nigeria (39% penetration) or DRC (28% penetration). The one metric where Senegal stands out is the ICT regulatory tracker with a score of 87 in 2022. An ITU report about Senegal’s telecom market[^38] mentions several key factors that led to high internet penetration rates.
{: style="text-align: justify;"}
- Early liberalization of the telecom sector in the early 2000s led to dynamic market competition.
- Robust regulatory framework is developed and supported by a regulatory agency (ARTP). The Electronic Communications Code rolled out in 2018 facilitated infrastructure sharing among operators, consumer data protection policies and universal access programs to expand connectivity in rural areas.
- The government invests in various initiatives like PTN (a digital incubator for startups and innovation), UVS (a digital university promoting e-learning and digital literacy) or Sénégal Numérique 2025 strategy that aims to make the country a regional digital hub in the near future.
- Universal Service Fund (FDSUT) finances infrastructure projects, along with private companies, that include expansion of networks in rural and underserved areas, community internet centers providing online access and subsidies for mobile operators to provide coverage in remote locations.
- Price and international investments have been crucial in financing infrastructure and expanding services including telecom giants like Orange, Sonatel, Huawei, ZTE and private equity firms.
Senegal has adopted a mobile-first approach when expanding their services due to high scalability and lower deployment costs in remote areas than fixed broadband. As a result mobile internet penetration exceeds 100% and only five countries in Africa rank higher in mobile broadband subscribers. Senegal’s success demonstrates that with the right policies and investment strategies, even countries facing economic constraints can create a more inclusive digital economy for their citizens.
{: style="text-align: justify;"}
The four Asian and Oceanic countries in this section (**Yemen, Pakistan, Afghanistan, Papua New Guinea**) are among the least developed countries in the world. Considering the socio-economic measures, both Yemen and Afghanistan appear similar including population numbers. Within Asia, Afghanistan had the lowest GDP PPP per capita as of 2023 and Yemen had the lowest HDI as of 2022. Both countries experience extreme gender inequality due to cultural norms and legal restrictions that disproportionately affect women. In fact, Yemen ranked last in GII and Afghanistan was number five from the bottom in 2022. In both countries more people lived in rural areas as of 2023 but in Afghanistan urban population constituted 27% while in Yemen urban population constituted 40% of total. As of 2023, Afghanistan favored mobile services with 56% mobile broadband penetration (compared to Yemen’s 34%) whereas Yemen favored fixed services with 1.27% penetration (compared to Afghanistan’s 0.08%). One metric where both countries differ is the ICT regulatory tracker. While Yemen ranked poorly in 2022 with 15 points, Afghanistan had a more modern framework with a score of 68 as of 2022. These challenges are worsened by ongoing instabilities such as the civil war in Yemen and fights between the Taliban and Islamic State in Afghanistan. Even with proper regulations in place, neither country will be able to expand digital services unless political issues are resolved and lasting security is established. Pakistan is one of the largest economies in Asia due to its large population of around 250 million. Even though it doesn’t have some of the challenges of its neighboring countries like Afganistan and Turkmenistan, it is still one of the least developed states in Asia with third-lowest HDI of 0.54 and fourth-highest GII of 0.52 (per 2022 data). Most people in Pakistan live outside of the cities where internet availability is scarce and expensive. Despite a solid regulatory foundation, the digital sector is not investor-friendly due to frequent censorship and shutdowns and low digital literacy.[^39] Papua New Guinea (PNG) has the lowest usage rate in the Oceanic region - only 24% of the population had accessed the internet in 2023. Most of the challenges are associated with geographical and social barriers. PNG is an island nation with mountainous terrain, remote villages where only 14% of Papuans lived in towns in 2023, making it difficult and expensive to lay fiber-optic cables or build cell towers. PNG lacks strong regulatory policies, reflected in a below-average score of 60 as of 2022. There are nearly 850 languages spoken on the island, making it the most linguistically diverse place on earth but also a barrier when it comes to expansion of online services as native language content is not easily available on the internet. Furthermore, some Papuans have never had any contact with the outside world and the country is often shaken by tribal violence which doesn’t create a conducive environment for increasing internet usage.[^40]
{: style="text-align: justify;"}
Even though neither **India** nor **China** are considered as countries with low internet penetration rates and even though they have made considerable progress in expanding online access, they continue to have a profound impact on the global digital divide due to the sheer size of their populations. The combined number of people in India and China without access to the Internet was over 950 million in 2023 which surpasses the number of people offline residing in the 24 least connected countries. In China, the main obstacles for expanding internet access are weak regulatory framework and government policies which was reflected in a low ICT tracker score of 60 as of 2022. MERICS (2023)[^41] explains in one of their reports that under the current government, economic actors are no longer guided by market-based incentives, but instead are steered by priorities set by the Chinese Communist Party. This creates a system where investments are concentrated in nationally significant sectors rather than less critical and low-profit but socially essential areas like rural internet expansion. Priority infrastructure projects are located in strategic city regions and not provinces that are home to minority and low-income population. Moreover, companies in the telecom sector are subject to constraints like access and information control that reduce the profitability of broadband services and make investments in the sector risky for private and foreign entities. In India, on the other hand, the constraints are socio-economic in nature. Relatively low HDI and high GII indicate that digital literacy is not widespread especially among minorities, women and low-income population. Even though mobile broadband services are relatively cheap, some people still cannot afford the devices or plans due to low income levels. As of 2023 64% of the population lives outside of the cities where infrastructure expansion is expensive and difficult. While the challenges both countries face differ (regulatory and policy-driven barriers in China and socio-economic and infrastructural limitations in India) the potential for progress is enormous. If either country is able to improve internet accessibility by even a small percentage, the increase in absolute numbers would be immense and it could reshape the global digital landscape.
{: style="text-align: justify;"}

[^24]: Source: [Institute of Electrical and Electronics Engineers, Digital Divide in Developing Countries: Why We Need to Close the Gap](https://ctu.ieee.org/blog/2023/01/23/digital-divide-in-developing-countries-why-we-need-to-close-the-gap/)

[^25]: Source: [National Broadband Network, About NBN Co](https://www.nbnco.com.au/corporate-information/about-nbn-co)

[^26]: Source: [US Telecom, Broadband Companies Invested $94.7B In U.S. Communications Infrastructure In 2023](https://www.ustelecom.org/broadband-companies-invested-94-7b-in-u-s-communications-infrastructure-in-2023-second-highest-annual-capex-in-22-years/)

[^27]: Source: [European Commission, Competition Policy, Sector inquiry into local loop](https://competition-policy.ec.europa.eu/sectors/electronic-communications-archived-redirected/sector-inquiry-local-loop_en)

[^28]: Source: [European Commission, Digital Strategy, EU Electronic Communications Code](https://digital-strategy.ec.europa.eu/en/policies/eu-electronic-communications-code)

[^29]: Source: [e-Estonia: Story](https://e-estonia.com/story/)

[^30]: Source: [NEOM, What is NEOM?](https://www.neom.com/en-us/about)

[^31]: Source: [Página 12, ¿En qué consiste el Plan Conectar?, 2020](https://www.pagina12.com.ar/292504-en-que-consiste-el-plan-conectar)

[^32]: Source: [Open Falklands, Fibra Optica Austral; Chile’s cable to Tierra del Fuego, 2019](https://openfalklands.com/fibra-optica-austral-connecting-northern-chile-to-tierra-del-fuego%EF%BB%BF/)

[^33]: Source: [UN Digital Development Observatory, Digital agenda for Latin America and the Caribbean](https://desarrollodigital.cepal.org/en/elac)

[^34]: Source: [UN Digital Development Observatory, Connectivity gaps as a factor of exclusion](https://desarrollodigital.cepal.org/en/data-and-facts/connectivity-gaps-factor-exclusion)

[^35]: Source: [CIA, The World Factbook, Turkmenistan, Communications](https://www.cia.gov/the-world-factbook/countries/turkmenistan/#communications)

[^36]: Source: [D. Garcia-Swartz, M. Campbell-Kelly (2022), Cellular: An Economic and Business History of the International Mobile-Phone IndustryOpen Access](https://direct.mit.edu/books/oa-monograph/5457/CellularAn-Economic-and-Business-History-of-the)

[^37]: Source: [ITU Collaborative Digital Regulation Country Reviews, Collaborative regulation in the Democratic Republic of Congo (Available in French), (2022)](https://www.itu.int/en/ITU-D/Regulatory-Market/Pages/collaborative-regulation-country-reviews/default.aspx)

[^38]: Source: [ITU Collaborative Digital Regulation Country Reviews, Collaborative regulation in Senegal (Available in French), (2022)](https://www.itu.int/en/ITU-D/Regulatory-Market/Pages/collaborative-regulation-country-reviews/default.aspx)

[^39]: Source: [Information Technology & Innovation Foundation, Disconnected Progress: The Hidden Price of Internet Restrictions in Pakistan, 06 Jan 2025](https://itif.org/publications/2025/01/16/disconnected-progress-the-hidden-price-of-internet-restrictions-in-pakistan/)

[^40]: Source: [The Economist, Papua New Guinea’s incredible linguistic diversity, Jul 20, 2017](https://www.economist.com/the-economist-explains/2017/07/20/papua-new-guineas-incredible-linguistic-diversity)

[^41]: Source: [Mercator Institute for China Studies, The party knows best: Aligning economic actors with China's strategic goals, Oct 12, 2023](https://merics.org/en/report/party-knows-best-aligning-economic-actors-chinas-strategic-goals)

## Recommendations
Bridging the digital divide requires focusing on factors that show a strong correlation with internet penetration in regions with low connectivity. However, not all factors are equally within a country’s control. For instance, introducing new policies is often more feasible than significantly increasing GDP. Therefore, in addition to examining correlations, it is important to prioritize areas that offer the highest impact relative to the required effort. Additionally, to ensure feasibility, the recommendations are derived from the success stories of countries with similar socio-economic and geographical situation to the ones that are currently struggling with internet access. These efforts include:
{: style="text-align: justify;"}
1. **Investments in infrastructure** to deploy or modernize electrical grids and telecommunications networks. Reliable electricity is a prerequisite for internet access and broadband infrastructure development should be determined based on the economic and geographical factors like affordability, urbanization or topography. For example, mobile technologies, albeit less reliable, are more cost-efficient in places with rugged terrain, scarce infrastructure or rural areas.
2. **Implementing educational programs** to equip individuals with necessary digital skills. Simply providing access does not guarantee digital inclusion if people do not see the benefits of internet or do not know how to navigate through the digital world because of lack of education or lack of content in their language. As of 2021, 40% of the global population was within mobile internet coverage but did not use the service.[^42] This figure shows that there is a big opportunity to expand internet penetration through promoting digital literacy.
3. **Formulating and enforcing policies** that encourage investment, competition, and innovation in the ICT sector. Governments can achieve this by introducing tax incentives for private telecom investments, streamlining licensing procedures, encouraging public-private partnerships and offering subsidies for broadband expansion in underserved areas. Those policies, in hand with infrastructure investments, should lead to affordable home access and complimentary access in public areas.
4. **Working towards political stability and fair access.** In regions plagued by conflicts or governed by restrictive regimes, promoting long-term political stability  and anti-discriminatory rights should be prioritized. International pressure, diplomatic engagement and civil advocacy can help counter censorship and access restrictions.
{: style="text-align: justify;"}

## Limitations
While this analysis offers important insights into the global penetration trends and their driving forces, it is important to acknowledge the following limitations related to data availability and consistency across countries that should be considered when interpreting the results.
{: style="text-align: justify;"}
1. The internet penetration data for some countries and years is either missing or the number provided is an ITU estimate. Specifically, over half of the observations are missing [fixed-broadband price baskets](#cost_ppp) and [population with higher degree completed](#pop_high_edu). Additionally, countries self-report the metrics which means that there might be different methodologies for calculating penetration.
2. Digital literacy data is crucial for the purpose of this analysis, yet it is not easily available. To measure it, I used are proxy metrics like `HDI` or `POP_HIGH_EDU` but they do not capture the knowledge specifically related to internet and IT.
3. Some socio-political and regulatory factors are difficult to quantify but still influential. Examples include digital censorship, regulatory environments that limit market competition or discourage investment in the telecommunications sector. Here I used metrics like the `ICT_TRACKER`, although the methodology documentation states that the tracker does not measure the quality or performance of regulatory measures in place.[^14] Some of the mentioned variables are not represented in the dataset but can affect internet accessibility as evidenced by cases from countries experiencing connectivity challenges.
4. Correlation does not imply causation. In this analysis, I compared the correlations between internet penetration and variables potentially influencing connectivity. Further research, including experimentation, is required to confirm the causal relationship between dependent and independent variables.
{: style="text-align: justify;"}

## Improvement suggestions
While the current analysis delivers valuable findings, there are opportunities for enhancements such as the ones proposed below.
{: style="text-align: justify;"}
1. Current free datasets lack measures that can quantify the effects of policies, availability of relevant content or connection speed and reliability to name a few examples. Portals like Statista offer more relevant datasets only to their paid customers. On the other hand, organizations like *The Economist* measure detailed factors like literacy, policy and content relevance but publish them as a composite Inclusive Internet Index.[^43] It would be worth exploring either paid data sources that offer more measures or obtaining raw data that goes into calculating the composite index.
2. Machine learning models can be used to expand the analysis through simulating hypothetical scenarios for countries with low penetration. For example, a panel regression model can be created to simulate the effect of increasing the ICT tracker score on internet usage. Alternatively, recommendation models can help with suggesting actions for low-penetration countries based on countries with similar profiles but higher penetration.
3. Creating forward-looking projections for the next few years would add more depth and help answer the question of how big is the potential for growth in developed vs developing countries as well as show how quickly the digital gap can close. This can be done using time series forecasting methods like ARIMA or LSTM models.
{: style="text-align: justify;"}

[^42]: Source: [GSMA, Too many are still offline: Global figures mask regional and demographic disparities, 10 Apr, 2025](https://www.gsma.com/solutions-and-impact/connectivity-for-good/mobile-for-development/blog/too-many-are-still-offline-global-figures-mask-regional-and-demographic-disparities/)
[^43]: Source: [The Economist, The Inclusive Internet Index](https://impact.economist.com/projects/inclusive-internet-index/)

## References

