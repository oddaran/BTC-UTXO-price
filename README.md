  Bitcoin is thought of as a medium of exchange, due to its layer on the internet having an immutable blockchain ledger, cryptographic security protocol, a predetermined supply free of centralized control, and permanence of transaction.  It also carries weight as a form of currency, able to transact in very small increments, allowing the cryptocurrency to be practical and ubiquitous as a global money.  One issue with using Bitcoin (BTC) is the price volatility.  While BTC is valued at $9,065 today, it has swung from a 2018 low of $3,232 to a high of $12,907 in 2019.  Within the blockchain structure of BTC lies a feature, unspent transaction output (UTXO) which acts as digital signature from sender to receiver.  This paper will attempt to correlate historic unspent transaction outputs of BTC in order to determine its potential for predicting BTC price volatility.

A.	Digital signature of BTC
  To track the transactions of each Bitcoin address, Bitcoin is designed with an architecture that avoids a potential problem in the banking industry known as double spending.  This problem is solved using an accounting structure called unspent transaction output, or UTXO.  Each transaction of every block record of state includes the input, and the output via this structure.  Unspent transaction outputs are broken up so that the correct amount, including fees, are distributed while the remaining value of the Bitcoin is returned to the sender as change (see figure 1).  Across time, it may be used as meaningful intelligence to understanding Bitcoin pricing.

B.	Choice of analytical tool
  R software was chosen, due to its bevy of statistical packages and popularity in evaluating markets within the finance industry (data mining, technical trading, and performance analysis).  R can also directly import real-time data from stock market indices (yet such data for Bitcoin was not available).  R also allows for creating easy and customizable graphic charts and figures, including time series plots.

C.	Bitcoin datasets
  Using Blockchain.com (2020) data, I gathered UTXO and USD price data for the preceding two years (March 13, 2018 to March 4, 2020).  Below is a sample of the raw data from both Excel sheets (blockchain.com, 2020):

D.	Analysis and visualizations
  Using Excel, data was prepared by taking weekly averages of UTXO and prices in US dollars.  The data was then combined into a single Excel sheet, and imported into R.  I performed manipulation of the factor column data into dates.  Then I used R basic functions to generate time series plots, from which R users could forecast price performance (Zhang, 2016).

# Import the bitcoin excel values
btcdata <- read.table(file="btc_utxo_price", sep = "\t", header=TRUE)
#find descriptive stats
summary(btcdata)

# find variance and std deviation
var(btcdata)
sd(btcdata)
#convert factor to date data
as.Date(btcdata$endweek, format = "%m/%d/%y")

# Visualize time series and plots
library(ggplot2)
# Begin by collapsing two series with tidyr
library(tidyr)
library(dplyr)

# Single line ts plot utxo count
p1 <- ggplot(btcdata, aes(x=endweek, y=utxo_average)) +
  geom_line(color = "#33CC33", size = 2) + 
  xlab("Date")
p1 <- p1 + scale_x_date(date_labels = "%b-%y", date_breaks = "6 months")
p1 <- p1 + labs(title = "BTC Unspent Transaction Output",
  caption = "Source: blockchain.info")
print(p1)

# Single line ts plot price
p2 <- ggplot(btcdata, aes(x=endweek, y=price_usdavg)) +
  geom_line(color = "#0066FF", size = 2) + 
  xlab("Date")
p2 <- p2 + scale_x_date(date_labels = "%b-%y", date_breaks = "6 months")
p2 <- p2 + labs(title = "BTC Price (USD)",
  caption = "Source: blockchain.info")
print(p2)

# Multiple ts plot change aesthetics and labels
chart <- btcdata %>%
  select(endweek, utxo_average, price_usdavg) %>%
  gather(key = "variable", value = "value", -endweek)
head(chart, 3)
g <- ggplot(chart, aes(x = endweek, y = value)) + 
  geom_line(aes(color = variable), size = 1) +
  scale_color_manual(values = c("#33CC33", "#0066FF")) +
  theme_minimal()
g <- g + labs(title = "BTC Unspent Transaction Output", 
  caption = "Source: blockchain.info")

E.  Applying R time series with normal distribution
  As one can see, mapping both plots of unspent transaction output (scale 40-67 million) and dollar prices of BTC (scale $3000-$13000) was impractical to show visual correlation.  Changing the y-axis scale aesthetics did not yield adequate plots.  This required knowledge of high-level plotting techniques.  I sought to correlate the data using statistical packages built into R.  

  I evaluated the usefulness of the given continuous data by testing for correlation assumptions.  This is performed by visually scatter plotting the UTXO/prices to check for linearity between them.  Then, using normality plots with the ggpubr library, I can discover whether the data falls under a normal distribution (CRAN, 2018):

# Correlate the ts plots by determining linearity and nml dist
library("ggpubr")
# utxo
ggqqplot(btcdata$utxo_average, ylab = "UTXO")
# price
ggqqplot(btcdata$price_usdavg, ylab = "Price USD")

# Use Spearman rank correlation coefficient
rank <-cor.test(btcdata$utxo_average, btcdata$price_usdavg, method = "spearman")
rank
ggscatter(btcdata, x = "utxo_average", y = "price_usdavg", 
          add = "reg.line", conf.int = TRUE, 
          cor.coef = TRUE, cor.method = "spearman",
          xlab = "Unspent transaction output", ylab = "Price in USD")
          
The correlation coefficient between x and y are 0.6457 and the p-value is < 2.2-16.  The test indicates a moderately positive correlation—signifying prices of BTC increases with unspent transaction outputs of BTC.

F.	Summary
  The nature of Bitcoin historically shows swings of volatility from one year to the next.  UTXOs may become a unique indicator of buy/sell pressure in the market for Bitcoin exchanges.  BTC does show price increases that are moderately correlated to amount of BTC unspent transaction outputs (UTXO).  Indeed, the analysis would yield more reliable results if more historical UTXO/price data were used.  With use of real-time transaction data, the model may undergo forecasting by extrapolating days, weeks, or even months to show asset managers and industry analysts whether to invest more or less proportions of Bitcoin for their portfolio.  BTC would not only provide an excellent medium of exchange, but also signal measures that could properly mitigate risk of investing in the cryptocurrency. 
