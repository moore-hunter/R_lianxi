# R_lianxi

setwd("D:/0000")
setwd("D:\、0000\\GitHub\\R_lianxi")
library(reshape)
dstats <- function(x)(  c(n=length(x) , mean=mean(x) , sd=sd(x) ) )
dfm <- melt( mtcars , measure.vars = c("mpg","hp","wt") ,
             id.vars = c("am","cyl")  )
dfm
cast( dfm , am+cyl+variable ~ ., dstats)
library(vcd)
head(Arthritis)
mytable <- with( Arthritis , table(Improved) )
mytable

table(Arthritis$Improved)
prop.table(mytable)
prop.table(mytable)*100
mytable <- xtabs( ~ Treatment+Improved , data = Arthritis)
mytable
margin.table( mytable , 1)
prop.table( mytable , 1)
margin.table( mytable , 2)
prop.table( mytable , 2)
addmargins( mytable)
addmargins( prop.table(mytable) )
addmargins( prop.table(mytable , 1) ,2 )
addmargins( prop.table(mytable , 2) ,1 )
library(gmodels)
mytable <- xtabs( ~Treatment+Sex+Improved , data = Arthritis)
mytable
ftable(mytable)
margin.table(mytable , 1)
margin.table(mytable , 2)
margin.table(mytable , 3)
margin.table(mytable , c(1,3))
ftable( prop.table( mytable , c(1,2) ) )
library(vcd)
mytable <- xtabs( ~Treatment + Improved , data = Arthritis)
chisq.test(mytable)
mytable <- xtabs( ~Improved + Sex, data = Arthritis)
chisq.test(mytable)
mytable <- xtabs( ~Treatment + Improved , data = Arthritis)
fisher.test(mytable)
mytable <- xtabs( ~Treatment + Improved +Sex, data = Arthritis)
mantelhaen.test(mytable)
mytable <- xtabs( ~Treatment + Improved , data = Arthritis)
assocstats(mytable)


table2flat <- function(mytable) {
                 df <- as.data.frame(mytable)
                 rows <- dim(df)[1]
                 cols <- dim(df)[2]
                 x <- NULL
                 for ( i in 1:rows) {
                          for ( j in 1:df$Freq[i]){
                                    row <- df[ i , c(1: (cols - 1) ) ]
                                    x <- rbind(x , row)
                                                  }
                                     }
                 row.names(x) <- c( 1:dim(x)[1] )
                 return(x)
                                  }

treatment <- rep(c("Placebo", "Treated"), times=3)
improved <- rep(c("None", "Some", "Marked"), each=2)
Freq <- c(29,13,7,17,7,21)
mytable <- as.data.frame(cbind(treatment, improved, Freq))
mytable
mydata <- table2flat(mytable)
library(psych)
states <- state.x77[ ,1:6]
cov(states)
cor(states)
cor(states , method = "spearman")
x <- states[,c("Population", "Income", "Illiteracy", "HS Grad")]
y <- states[,c("Life Exp", "Murder")]
cor( x , y)
library(ggm)
pcor( c(1,5,2,3,6) , cov(states) )
cor.test( states[,3] , states[,5] )
library(psych)
corr.test( states , use = "complete")
library(MASS)
t.test( Prob ~ So , data = UScrime)
sapply( UScrime[ c("U1","U2") ] , function(x) ( c(mean=mean(x) ,sd=sd(x) ) ) )
with( UScrime , t.test( U1 ,U2 , paired = TRUE) )
with( UScrime , by( Prob , So , median) )
wilcox.test( Prob ~ So , data = UScrime)
sapply( UScrime[ c("U1","U2") ] , median)
with( UScrime , wilcox.test( U1 , U2 ,paired = TRUE) )
states <- as.data.frame( cbind(state.region , state.x77) )
fit <- lm( weight ~ height , data = women)
summary(fit)
women$weight
fitted(fit)
residuals(fit)
fit2 <- lm( weight ~ height + I(height^2) , data = women)
summary(fit2)
plot( women$height , women$weight ,
      xlab = "height" , ylab = "weight" )
lines( women$height , fitted( fit2))
abline(fit2)
fit3 <- lm( weight ~ height + I(height^2)+I(height^3) , data = women)
plot( women$height , women$weight ,
      xlab = "height" , ylab = "weight" )
lines( women$height , fitted( fit3))
library(car)
scatterplot( weight ~ height , data = women, spread = FALSE ,
             lty.smooth = 2 , pch = 19 ,
             main = "women" , xlab = "height" , ylab = "weight" )
str(state.x77)
states <- as.data.frame( state.x77[ ,c("Murder","Population","Illiteracy",
                                       "Income","Frost") ] )
states
cor(states)
library(car)
scatterplotMatrix( states , spread = FALSE , lty.smooth = 2)
fit <- lm( Murder ~ Population+Illiteracy+Income+Frost , data=states)
summary(fit)
fit <- lm( mpg~ hp+wt+hp:wt , data = mtcars)
summary(fit)
fit <- lm(Murder ~ Population + Illiteracy + Income + Frost, data=states)
confint(fit)
fit <- lm(weight ~ height, data=women)
par(mfrow=c(2,2))
fit2 <- lm(weight ~ height + I(height^2), data=women)
par(mfrow=c(2,2))
newfit <- lm(weight~ height + I(height^2), data=women[-c(13,15),])
fit <- lm(Murder ~ Population + Illiteracy + Income + Frost, data=states)
par(mfrow=c(2,2))
library(car)
fit <- lm(Murder ~ Population + Illiteracy + Income + Frost, data=states)
states["Nevada" ,]
fitted(fit)["Nevada"]
residuals(fit)["Nevada"]
rstudent(fit)["Nevada"]


residpot <- function( fit , nbreaks = 10) {
                           z<- rstudent(fit)
                           hist(z , breaks = nbreaks , freq = FALSE ,
                                  xlab = "sr" , main = "dor" )
                           rug(jitter(z) , col="brown")
                           curve( dnorm(x , mean=mean(z) , sd =sd(z) ) ,
                                  add = TRUE , col="blue" , lwd = 2)
                           lines( density(z)$x , density(z)$y ,
                                  col = "red" , lwd = 2 , lty = 2)
                                                 }



legend( "topright", legend=c("normal","kernel") ,
        lty =1:2 , col = c("blue","red") , cex = 0.7 )
durbinWatsonTest(fit)
library(car)
crPlots(fit)
ncvTest(fit)
spreadLevelplot(fit)
spreadLevelPlot(fit)
library(gvlma)
gvlmodel <- gvlma(fit)
summary(gvlmodel)
vif(fit)
sqrt( vif(fit) )
library(car)
outlierTest(fit)


hat.plot <- function(fit) {
                    p <- length( coefficients(fit) )
                    n <- length( fitted(fit) )
                    plot( hatvalues(fit) , main = "edfg" )
                    identify( 1:n , hatvalues(fit) , names( hatvalues(fit) ) )
                                 }



cutoff <- 4/( nrow(states) - length(fit$coefficients) - 2)


avPlots(fit  , ask = FALSE , onepage = TRUE , id.method = "identify")
library(car)
avPlots(fit  , ask = FALSE , onepage = TRUE , id.method = "identify")

influencePlot( fit , id.methos = "identify" , main ="influence plot" ,
                sub="EDFDGTF" )
library(car)
summary(powerTransform(states$Murder))
boxTidwell( Murder~Population + Illiteracy  , data = states)
influencePlot( fit , id.method = "identify" , main ="influence plot" ,
               sub="EDFDGTF" )

fit1 <- lm(Murder ~ Population + Illiteracy + Income + Frost, data= states)
library(car)
fit1 <- lm(Murder ~ Population + Illiteracy + Income + Frost, data= states)
library(MASS)
fit1 <- lm(Murder ~ Population + Illiteracy + Income + Frost, data = states)
states
fit1 <- lm( Murder ~ Population+Illiteracy+Income+Frost , data=states)

states <- state.x77[ ,1:6]

states <- as.data.frame( cbind(state.region , state.x77) )
fit1 <- lm( Murder ~ Population+Illiteracy+Income+Frost , data=states)
stepAIC(fit  , direction = "backward")
stepAIC(fit1  , direction = "backward")
library(leaps)
leaps <- regsubsets( Murder ~ Population + Illiteracy + Income + Frost ,
data = states , nbest = 4)
plot( leaps , scale = "adjr2")
library(car)
subsets( leaps , statistic = "cp" , main = "ewdfe")
abline( 1 ,1 ,lty = 2 ,col = "red")
plot( leaps , scale = "adjr2")
subsets( leaps , statistic = "cp" , main = "ewdfe")
abline( 1 ,1 ,lty = 2 ,col = "red")


shrinkage <- function( fit , k = 10) {
                          require(bootstrap)
                          theta.fit <- function(x , y){lsfit(x,y)}
                          theta.predict <- function( fit , x){
                                                              cbind(1,x)%*%fit$coef }
                          x <- fit$model[ , 2:ncol(fit$model) ]
                          y <- fit$model[,1]
                          results <- crossval( x ,y, theta.fit , theta.predict , ngroup = k)
                          r2 <- cor( y , fit$fitted.values)^2
                          r2cv <- cor( y , results$cv.fit)^2
                          cat("original r-square" , r2 ,"\n")
                          cat( k , "fold cross-validated r-square = " ,r2cv , "\n")
                          cat("change=" , r2-r2cv , "\n")
                                          }


fit <- lm(Murder ~ Population + Income + Illiteracy + Frost, data=states)
shrinkage(fit)
library(bootstrap)
require(bootstrap)
theta.fit <- function(x , y){lsfit(x,y)}
theta.fit <- function(x , y){lsfit(x,y)}
fit2 <- lm(Murder~Population+Illiteracy,data=states)
zstates <- as.data.frame( scale(states) )
zfit <- lm(Murder~Population + Income + Illiteracy + Frost, data=zstates)
coef(zfit)

relweights <- function(fit,...){
                        R <- cor(fit$model)
                        nvar <- ncol(R)
                        rxx <- R[2 :nvar , 2:nvar]
                        rxy <- R[2 :nvar , 1]
                        svd <- eigen(rxx)
                        evec <- svd$vectors
                        ev <- svd$values
                        delta <- diag( sqrt(ev) )
                        lambda <- evec %*% delta %*% t(evec)
                        lambdasq <- lambda^2
                        beta <- solve(lambda) %*% rxy
                        resquare <- colSums(beta^2)
                        rawwgt <- lambdasq %*% beta ^2
                        import <- (rawwgt / resquare)*100
                        lbls <- names( fit$model[2:nvar] )
                        rownames(import) <- lbls
                        colnames(import) <- "weight"
                        barplot( t(import) , names.arg = lbls ,
                                 ylab = "% of r-square"
                                ,xlab = "predictor variables" ,
                                 main = "relativew importance of predict " ,
                                 sub = paste("r-square = " , round(resquare , digits = 3)) ,
                                  ...)
                        return(import)
                                     }
relweights(fit , col="lightgrey")


library(multcomp)
attach(cholesterol)
table(trt)
aggregate( response , by = list(trt) , FUN =mean)
aggregate( response , by = list(trt) , FUN =sd)
fit <- aov( response ~ trt)
summary(fit)
library(gplots)
plotmeans( response~ trt , xlab = "fr" ,ylab="rd" , main = "rfgh")
detach(cholesterol)
TurkeyHSD(fit)
TukeyHSD(fit)
par(las = 2)
par( mar = c(5,8,4,2) )
plot( TukeyHSD(fit) )
library(multcomp)
par( mar = c(5,4,6,2) )
tuk <- glht( fit , linfct = mcp(trt = "Tukey" ) )
plot( cld( tuk , levels = 0.05 ) , col = "lightgrey" )
library(car)

qqPlot(lm(response ~ trt, data=cholesterol),
simulate=TRUE, main="Q-Q Plot", labels=FALSE)

bartlett.test( response ~  trt , data = cholesterol)
library(car)
outlierTest(fit)
data(litter , package ="multcomp")
attach(litter)
table(dose)
aggregate( weight , by = list(dose) , FUN = mean)
fit <- aov( weight ~ gesttime + dose)
summary(fit)

library(effects)

effect("dose" , fit)
data(litter , package ="multcomp")
attach(litter)
table(dose)
aggregate( weight , by = list(dose) , FUN = mean)
fit <- aov( weight ~ gesttime + dose)
summary(fit)
effect("dose" , fit)

library(multcomp)
contrast <- rbind( "no drug vs . dryg" = c( 3,-1,-1,-1) )
contrast
summary( glht( fit ,  linfct = mcp( dose = contrast) ))

fit2 <- aov( weight ~ gesttime*dose , data = litter)
summary(fit2)
library(HH)
ancova( weight ~ gesttime + dose , data = litter)
ancova( weight ~ gesttime*dose , data = litter)

ancova( weight ~ gesttime*dose , data = litter)
attach(ToothGrowth)
ToothGrowth
table(supp , dose)
aggregate( len , by = list( supp , dose) , FUN= mean)
aggregate( len , by = list( supp , dose) , FUN= sd)
fit <- aov( len~ supp*dose)

summary(fit)
interaction.plot( dose , supp , len , type = "b" ,
                  col = c("red", "blue") , pch = c(16,18) ,
                  main = "inedfr"  , xlab = "rfss" )

attach(ToothGrowth)
table( supp , dose)
center <- colMeans(y)
library(MASS)
attach(UScereal)
y <- cbind(calories , fat , sugars)
center <- colMeans(y)
n <- nrow(y)
p <- ncol(y)
cov <- cov(y)
d <- mahalanobis(y,center,cov)
abline( a = 0 , b = 1)

coord <- qqplot( qchisq(ppoints(n) ,df = p) , d,
                 main = "edfs" , ylab = "sdd" )
abline( a = 0 , b = 1)
identify(coord$x , coord$y , labels = row.names(UScereal))
library(mvoutlier)
ooutliers <- aq.plot(y)

library(rrcov)
Wilks.test(y , shelf , method = "mcd")
Wilks.test(y,shelf,method="mcd")
library(multcomp)
levels(cholesterol$trt)pwr.r.test(r=.05 , sig.level = .05 , power = .9 , alternative =  "greater")



fit.aov <- aov(response ~ trt , data = cholesterol)
summary(fit.lm)
summary(fit.aov)
fit.lm <- lm( response ~ trt , data = cholesterol)
summary(fit.lm)

contrast(cholesterol$trt)
contrasts(cholesterol$trt)


library(pwr)
pwr.t.test( d = .8 , sig.level = .05 , power = .9 , type = "two.sample",alternative = "two.sided")
pwr.t.test(n = 20 , d=.5 , sig.level = .01 , type = "two.sample" ,alternative = "two.sided")


pwr.t2n.test(n1 = 20,n2 = 35 , d=.5 , sig.level = .01  ,alternative = "two.sided")
pwr.t2n.test(n1 = 30,n2 = 35 , d=.5 , sig.level = .01  ,alternative = "two.sided")

pwr.r.test(r=.05 , sig.level = .05 , power = .9 , alternative =  "greater")
pwr.r.test(r=.25 , sig.level = .05 , power = .9 , alternative =  "greater")
pwr.2p.test(h = ES.h(.65 , .6) , sig.level = .05 , power =  .9, alternative="greater")

pwr.r.test(r=.05 , sig.level = .05 , power = .9 , alternative =  "greater")
prob <- matrix(c(.42 ,.28 , .03 ,.07 , .10 , .10) , byrow = TRUE , nrow = 3)
ES.w2(prob)

pwr.chisq.test(w=0.1853 ,df = 2 , sig.level = 0.05 ,power= 0.9)

library(pwr)
es <- seq( 0.1 ,0.2 ,0.01)
nes <- length(es)
samsize <- NULL

for ( i in 1:nes){
                  result <- pwr.anova.test(k=5 ,f =es[i] ,sig.level = 0.05   ,power= 0.9)
                  samsize[i] <- ceiling(result$n)
                      }


plot(samsize ,es,type="l" , lwd = 2 , col = "red" ,
     ylab ="effect size" , xlab = "sample size" ,
     main = "one way anova" )


r <- seq(0.1 , 0.5 , 0.01)
nr <- length(r)
p <- seq(0.4 ,0.9 ,0.1)
np <- length(p)
samsize <- array( numeric(nr*np) , dim = c(nr , np))


for(i in 1:np){
               for (j in 1:nr){  result <- pwr.r.test(n=NULL , r = r[j] ,
                                 sig.level = 0.05 ,
                                 power = p[i] ,
                                 alternative = "two.sided")
                                 samsize[j ,i] <- ceiling(result$n)
                                }
                }


xrange <- range(r)
yrange <- round( range(samsize) )
colors <- rainbow( length(p) )
plot( xrange , yrange , type = "n" , xlab = "correlation" ,
ylab = "sample size")


for (i in 1:np){
                lines(r,samsize[,i] , type ="l" , lwd=2 , col = colors[i])
                 }


abline(v=0 , h=seq(0,yrange[2],50), lty=2 , col="grey89")
abline(h=0 , v=seq(xrange[1],xrange[2],0.02), lty=2 , col="grey89")
title("sample size ~~~~~~~~")
legend("topright" , title = "power" , as.character(p) , fill = colors)

attach(mtcars)
plot(wt , mpg , main = "basic" , xlab = "car weight" , ylab = "miles per gallon" ,pch=19)
abline(lm(mpg~wt), col="red", lwd=2, lty=1)
lines( lowess(wt , mpg) , col="blue" , lwd = 2 , lty = 2)
library(car)
scatterplot(mpg ~ wt | cyl , data = mtcars ,lwd = 2 , main =  "2" ,xlab = "e",
            ylab = "f" ,legend.plot = TRUE , id.method = "identify" ,
labels = row.names(mtcars) , boxplots = "xy")
pairs( ~mpg+disp+drat+wt , data = mtcars , ,main = "baisc scatter plot matrix")
pairs( ~mpg+disp+drat+wt , data = mtcars , ,main = "baisc scatter plot matrix", upper.panel = NULL)
scatterplotMatrix(~mpg+disp+drat+wt ,data = mrcars ,spread=FALSE , lty.smooth =2, main = "scatter plot matrix via car package")
scatterplotMatrix(~mpg+disp+drat+wt ,data = mtcars ,spread=FALSE , lty.smooth =2, main = "scatter plot matrix via car package")
warnings()
scatterplotMatrix(~ mpg + disp + drat + wt, data=mtcars, spread=FALSE,
lty.smooth=2, main="Scatter Plot Matrix via car Package")
scatterplotMatrix(~ mpg + disp + drat + wt|cyl, data=mtcars, spread=FALSE,
diagonal = "histogram", main="Scatter Plot Matrix via car Package")
scatterplotMatrix(~ mpg + disp + drat + wt|cyl, data=mtcars, spread=FALSE,
diagonal = "histogram", main="Scatter Plot Matrix via car Package",                   by.groups = TRUE)
library(gclus)
cor( mtcars[ c("mpg" , "wt" , "disp" , "drat")])
mydata <- mtcars[ c(1,3,5,6)]
mydata.corr <- abs( cor(mydata))

mycolors <- dmat.color( mydata.corr)

myorder <- order.single( mydata.corr)
cpairs( mydata ,myorder , panel.colors = mycolors , gap = .5 , main = "v o a c b c")
cpairs( mydata ,myorder , panel.colors = mycolors , gap = .5 , main = "v o a c b c" ,upper.panel = NULL)
set.seed(1234)
n <- 10000
c1 <- matrix( rnorm(n , mean = 0 , sd = .5) ,ncol = 2)

c2 <- matrix( rnorm(n , mean =  3 , sd = 2) , ncol = 2)
mydata <- rbind(c1 ,c2)



mydata <- as.data.frame(mydata)

names(mydata) <- c("x" ,"y")
with( mydata , plot(x ,y ,pch = 19 , main = "scatter plot with 10000 obse"))
with( mydata , smoothScatter(x ,y ,pch = 19 , main = "scatter plot by smoothed densit"))

library(hexbin)

#重要视图
with( mydata , {bin <- hexbin(x,y , xbins =50)
      plot(bin ,main= "hexagonal binnning with 10K observ")   }  )


library(IDPmisc)
with(mydata , iplot(x,y,main = "image scatter plot with color ~~~~"))
library(scatterplot3d)

scatterplot3d(wt, disp, mpg,
              main="Basic 3D Scatter Plot")
scatterplot3d(wt ,disp , mpg , pch = 16, highlight.3d =  TRUE , type = "h")
fit <- lm(mpg ~ wt + disp)
s3d <- scatterplot3d(wt ,disp , mpg , pch = 16, highlight.3d =  TRUE , type = "h")
s3d$plane3d(fit)
library(rgl)



attach(mtcars)
plot3d(wt , disp , mpg , col = "red" , size = 5)

#气泡图
library(Rcmdr)
scatter3d(wt , disp , mpg)
r <- sqrt(disp/pi)
symbols(wt ,mpg ,circle = r , inches = 0.3 ,fg ="white" , bg = "lightblue")

symbols(wt ,mpg ,circle = r , inches = 0.3 ,fg ="white" , bg = "lightblue",
        main = "bubble plot with points size proportional to displacement",
        ylab ="miles per gallon" , xlab = "weight of car")
text( wt , mpg , rownames(mtcars) , cex = 0.6)

detach(mtcars)


opar <- par( no.readonly = TRUE)
par( mfrow = c(1,2))
t1 <- subset(Orange , Tree == 1)
plot( t1$age , t1$circumference , xlab = "age" , ylab = "cir" , main = "growth")
plot( t1$age , t1$circumference , xlab = "age" , ylab = "cir" , main = "growth", type = "b")
par(opar)




Orange$Tree <- as.numeric(Orange$Tree)
ntrees <- max(Orange$Tree)
xrange <- range(Orange$age)
yrange <- range(Orange$circumference)

plot(xrange, yrange,
    type="n",xlab="Age (days)",ylab="Circumference (mm)" )

colors <- rainbow(ntrees)
linetype <- c(1:ntrees)
plotchar <- seq(18, 18+ntrees, 1)

for (i in 1:ntrees) {
                     tree <- subset(Orange, Tree==i)
                     lines(tree$age, tree$circumference,
                            type="b",lwd=2,lty=linetype[i],col=colors[i],
                             pch=plotchar[i]                         )   }
title("Tree Growth", "example of line plot")
legend(xrange[1], yrange[2],
       1:ntrees,cex=0.8,col=colors,pch=plotchar,lty=linetype,title="Tree")


options(digits = 2)
cor(mtcars)
library(corrgram)
library(corrgram)
corrgram(mtcars ,order = TRUE ,lower.panel = panel.shade ,upper.panel = panel.pie ,text.panel = panel.txt ,main = "cor of mtcars intercor")
corrgram(mtcars,order = TRUE,lower.panel = panel.ellipse ,upper.panel = panel.pts ,text.panel = panel.txt ,diag.panel = panel.minmax ,main="sfes")
corrgram(mtcars, lower.panel=panel.shade,
upper.panel=NULL, text.panel=panel.txt,main="Car Mileage Data (unsorted)")



col.corrgram <- function(ncol){
                     colorRampPalette( c("drakgoldenrod4" , "burlywoodl","darkkhaki"                                            ,"darkgreen"))
                      (ncol)    }

corrgram(mtcars,order = TRUE ,lower.panel = panel.shade ,upper.panel = panel.pie ,text.panel = panel.txt , main ="sedcdf")

col.corrgram <- function(ncol){
          colorRampPalette( c("black" , "red","yellow"                                            ,"green"))
                           (ncol)    }
corrgram(mtcars,order = TRUE ,lower.panel = panel.shade ,upper.panel = panel.pie ,text.panel = panel.txt , main ="sedcdf")


ftable(Titanic)
library(vcd)
mosaic(mtcars)
mosaic(Titanic , shade = TRUE , legend = TRUE)
mosaic(~Class+Sex+Age+Survived , data = Titanic , shade = TRUE , legend =TRUE)



library(coin)
score <- c(40,57,45,55,58,57,64,55,62,65)
treatment <- factor( c( rep("A" ,5) , rep("B" ,5)))
mydata <- data.frame( treatment , score)
t.test(score ~ treatment , data = mydata , var.equal = TRUE)
mydata
oneway_test(score~treatment, data=mydata, distribution="exact")
library(MASS)
UScrime <- transform(UScrime , So = factor(So))
wilcox.test(Prob ~ So , data = UScrime , distribution ="exact")
library(multcomp)
set.seed(1234)
oneway_test(response~trt, data=cholesterol,
            distribution=approximate(B=9999))
library(coin)
library(vcd)
Arthritis <- transform(Arthritis , Improved = as.factor(as.numeric(Improved)))

set.seed(1234)
chisq_test(Treatment ~ Improved , data = Arthritis ,distribution=approximate(B=9999))



states <- as.data.frame(state.x77)
set.seed(1234)
spearman_test(Illiteracy ~ Murder , data = states ,distribution=approximate(B=9999))
wilcoxsign_test(U1 ~U2 , data = UScrime,distribution= "exact")


library(lmPerm)
set.seed(1234)
fit <- lmp(weight ~ height , data = women , perm = "Prob")
fit1 <- lmp(weight ~ height + I(height^2) , data = women , perm = "Prob")
fit3 <- lmp(Murder~Population+Illiteracy+Income+Frost , data = states , perm="Prob")


library(boot)
rsq <- function(formula ,data ,indices){
                        d <- data[indices , ]
                        fit <- lm(formula , data = d)
                        return( summary(fit)$r.square)
                                           }
set.seed(1234)
results <- boot(data = mtcars , statistic = rsq , R= 1000 , formula = mpg~wt+disp)


print(results)
plot(results)
boot.ci(results ,type = c("perc" , "bca"))
bs <- function(formula , data , indices){
                       d <- data[indices , ]
                       fit <- lm(formula , data = d)
                       return(coef(fit))
                                           }
results <- boot( data=mtcars , statistic = bs,R=1000,formula=mpg~wt+disp)
print(results)
plot(results , index = 2)
boot.ci(results , type = "bca" , index = 2)
boot.ci(results , type = "bca" , index = 3)



library(AER)
data( Affairs , package = "AER")
summary(Affairs)
table(Affairs$affairs)
Affairs$ynaffair[Affairs$affairs > 0 ] <- 1
Affairs$ynaffair[Affairs$affairs == 0 ] <- 0
Affairs$ynaffair <- factor(Affairs$ynaffair , levels = c(0,1) , labels = c("No","Yes"))
table(Affairs$ynaffair)
fit.full <- glm(ynaffair ~ gender + age +yearsmarried + children + religiousness + education + occupation + rating , data = Affairs , family = binomial())
summary(fit.full)
fit.reduced <- glm(ynaffair ~ age + yearsmarried + religiousness + rating ,family = binomial() , data = Affairs)
summary(fit.reduced)
anova(fit.reduced , fit.full , test = "Chisq")
coef(fit.reduced)
exp(coef(fit.reduced))
confint(fit.reduced)
exp(confint(fit.reduced))


testdata <- data.frame(rating=c(1,2,3,4,5) , age=mean(Affairs$age) , yearsmarried=mean(Affairs$yearsmarried) , religiousness = mean(Affairs$religiousness))
testdata
testdata$prob <- predict(fit.reduced , newdata = testdata , type = "response")
testdata
testdata
testdata$prob <- predict(fit.reduced , newdata = testdata , type = "response")
testdata <- data.frame(rating = mean(Affairs$rating) , age=seq(17,57,10) ,yearsmarried=mean(Affairs$yearsmarried) ,religiousness=mean(Affairs$religiousness))
testdata$prob <- predict(fit.reduced , newdata = testdata , type = "response")
testdata


fit <- glm(ynaffair ~ age+yearsmarried + religiousness+rating ,family = binomial() , data = Affairs)
fit.od <- glm(ynaffair ~ age+yearsmarried+religiousness+rating,family = quasibinomial() ,data = Affairs)
pchisq(summary(fit.od)$dispersion * fit$df.residual , fit$df.residual , lower = F)



library(robust)
data("breslow.dat")
names(breslow.dat)
summary(breslow.dat[ c(6,7,8,10) ])
opar <- par( no.readonly =  TRUE)
par(mfrow = c(1,2))
attach(breslow.dat)
hist(sumY , breaks = 20 , xlab = "seizure count" ,main = "distribution of seozure")
boxplot(sumY ~ Trt , xlab = "treatment" , main = "group comparisons")
par(opar)



fit <- glm(sumY ~ Base + Age + Trt , family = poisson() , data = breslow.dat)
summary(fit)
summary(fit)
coef(fit)
exp(coef(fit))

library(qcc)
qcc.overdispersion.test(breslow.dat$sumY , type = "poisson")





library(psych)
fa.parallel(USJudgeRatings[,-1], fa="PC", show.legend=FALSE, main="Scree plot with parallel analysis")
pc <- principal(USJudgeRatings[,-1] , nfactors = 1)
pc
fa.parallel(Harman23.cor$cov, n.obs=302, fa="pc", show.legend=FALSE, main="Scree plot with parallel analysis")
pc <- principal(Harman23.cor$cov , nfactors=2, rotate = "none")
pc
rc <- principal(Harman23.cor$cov , nfactors=2, rotate = "varimax")
rc


library(psych)
pc <- principal(USJudgeRatings[,-1] , nfactors = 1 , scores = TRUE)
head(pc$scores)
summary(pc)
cor(USJudgeRatings$CONT , pc$score)
rc <- principal(Harman23.cor$cov , nfactors = 2 , rotate = "varimax")
round(unclass(rc$weights) , 2)
options(digits = 2)
covariances <- ability.cov$cov
correlations <- cov2cor(covariances)
correlations
library(psych)
covariances <- ability.cov$cov
correlations <- cov2cor(covariances)

fa.parallel(correlations, n.obs=112, fa="both",main="Scree plots ")


library(psych)
covariances <- ability.cov$cov
correlations <- cov2cor(covariances)
fa <- fa(correlations,nfactors = 2,rotate = "none" ,fm="pa")
fa
fa.varimax <- fa(correlations,nfactors = 2,rotate = "varimax" ,fm="pa")
fa.varimax
fa.promax <- fa(correlations,nfactors = 2,rotate = "promax" ,fm="pa")
fa.promax


fsm <- function(oblique){
               if(class(oblique)[2] == "fa"&is.null(oblique$Phi)){
                                        warning("object doesn't ~~")
                                                                      }
                else{
                     P <- unclass(oblique$loading)
                     F <- P %*% oblique$Phi
                     colnames(F) <- c("PA1" ,"PA2")
                     return(F)
                        }
                               }
fsm(fa.promax)


fa.diagram(fa.promax , simple = FALSE)
fa.diagram(fa.promax , simple = TRUE)
factor.plot(fa.promax , labels = rownames(fa.promax$loadings))


fa.24tests <- fa(Harman74.cor$cov , nfactors=4 ,rotate="promax")
fa.promax$weights


data(sleep ,package = "VIM")
sleep[complete.cases(sleep) ,]
sleep[!complete.cases(sleep) ,]

sum( is.na(sleep$Dream))
MEAN( is.na(sleep$Dream))
mean( is.na(sleep$Dream))
mean( !is.na(sleep$Dream))
library(mice)
data(sleep ,package = "VIM")
md.pattern(sleep)
library(VIM)
aggr(sleep , prop = FALSE , numbers = TRUE)
aggr(sleep , prop = TRUE , numbers = TRUE)
matrixplot(sleep)
marginplot( sleep[c("Gest", "Dream")] , pch = c(20),ccol = c("darkgrey","red" ,"blue"))


options(digits=1)
cor( na.omit(sleep))
cor( sleep , use = "complete.obs")


fit <- lm(Dream ~ Span + Gest , data=na.omit(sleep))
summary(fit)
fit1 <- lm(Dream ~ Span + Gest , data=sleep)
summary(fit1)


library(mice)
data(sleep , package = "VIM")
imp <- mice(sleep , seed = 1234)
fit <- with(imp , lm(Dream ~ Span + Gest))
pooled <- pool(fit)
summary(pooled)
imp
imp$imp$Dream
dataset31 <- complete(imp , action = 3)
dataset31
dataset32 <- complete(imp , action = 4)
dataset32
dataset33 <- complete(imp , action = 5)
dataset33

library(lattice)
attach(mtcars)
gear <- factor( gear , levels = c(3,4,5) ,
labels = c("3 gear","4 gear" ,"5 gear"))
cyl <- factor(cyl , levels = c(4,6,8),
labels = c("4 cylinders","6 cylinders" ,"8 cylinders"))
densityplot(~mpg|cyl )
densityplot(~mpg)
bwplot(cyl ~ mpg|gear)
xyplot(mpg~wt|cyl*gear)
cloud(mpg~wt*qsec|cyl)
dotplot(cyl~mpg|gear)
splom
splom(mtcars[c(1,3,4,5,6)])
detach(mtcars)


mygraph <- densityplot(~height|voice.part , data= singer)
mygraph
plot(mygraph)
update(mygraph , col="red",pch=16,cex=.8 ,jitter=.05 , lwd=2)


displacement <- equal.count(mtcars$disp , number=3 , overlap = 0)
xyplot(mpg~wt|displacement, data = mtcars,layout=c(3,1) , aspect = 1.5)


xyplot(mpg~wt|displacement, data = mtcars)
xyplot(mpg~wt|displacement, data = mtcars ,panel = panel.xyplot)

#使用自己创建的面板函数
mypanel <- function(x,y){
              #生成填充圆圈（pch=19）
               panel.xyplot(x,y,pch=19)
               #在每个面板的x，y轴添加轴须线
               panel.rug(x,y)
               #添加水平和竖直的网格线（使用负数强制轴标签对齐）
               panel.grid(h= -1 , v= -1)
               #添加一条红色回归线
               panel.lmline(x,y,col="red",lwd=1,lty=2)
                           }
xyplot(mpg~wt|displacement ,data = mtcars,layout=c(3,1),aspect = 1.5,panel = mypanel)


panel.smoother <- function(x,y){
                         panel.grid(h=-1 , v=-1)
                         #绘制各个点
                         panel.xyplot(x,y)
                         #绘制非参数拟合线
                         panel.loess(x,y)
                         #在条件变量的各个水平下添加mpg的均值线
                         panel.abline(h=mean(y),lwd=2,lty=2,col="green")
                                      }

xyplot(mpg~disp|transmission,data=mtcars,
         scales=list(cex=.8, col="red"),
         panel=panel.smoother)

#核密度图
densityplot(~mpg,data = mtcars,groups = transmission ,auto.key = TRUE)
auto.key=list(space ="right",columns=2 ,title="transmissions")



#自定义图例并含有分组变量的核密度图
library(lattice)
mtcars$transmission <- factor(mtcars$am ,levels =c(0,1),labels=c("Automatic","Manual"))
#设定颜色、线条和点类型(均被设置为向量)
colors = c("red","blue")
lines = c(1,2)
points = c(16,17)
#自定义图例（创建图例的列表对象）
key.trans <- list(title ="Transmission" , space = "bottom", columns = 2,
                   text=list( levels(mtcars$transmission) ),
                   points = list( pch=points , col = colors ),
                   lines = list( col = colors , lty = lines ),
                   cex.title=1 , cex = .9)
#自定义密度图
densityplot(~mpg, data=mtcars,
             group=transmission,pch=points, lty=lines, col=colors,
             lwd=2, jitter=.005,key=key.trans)


#包含分组变量和条件变量以及自定义图例的xyplot
library(lattice)
colors <- "darkgreen"
linetype <- c(1:3)
symbols <- c(1:12)
key.species <- list(title = "Plant" ,space = "right" ,text = list( levels(CO2$Plant)),
                    points = list(pch=symbols , col=colors) )

xyplot(uptake~conc|Type*Treatment , data = CO2, groups = Plant , type="o",
      pch=symbols ,col=colors , lty=linetype,
      ylab=expression(paste("Uptake ",
            bgroup("(", italic(frac("umol","m"^2)), ")"))),
      xlab=expression(paste("Concentration ",
            bgroup("(", italic(frac(mL,L)), ")"))),
      key=key.species)



par()
trellis.par.get()
show.settings()
mysettings <- trellis.par.get()
mysettings$superpose.symbol
mysettings$superpose.symbol$pch <- c(1:10)
trellis.par.set(mysettings)
show.settings()



library(lattice)
graph1 <- histogram(~height|voice.part , data = singer )
graph2 <- densityplot(~height , data = singer ,group = voice.part ,plot.points = FALSE ,
auto.key = list( columns = 4 )  )
#将页面分为一列两行的矩阵，并将图形放在第一列、第一行
plot(graph1, split = c(1,1,1,2))
plot(graph2 , split= c(1,2,1,2) ,newpage = FALSE)

#position = c(xmin, ymin, xmax, ymax)
plot(graph1 , position = c(0,.3,1,1))
plot(graph2 , position = c(0,0,1,.3) ,newpage = FALSE)



library(ggplot2)
#创建以汽缸数为条件的每加仑英里数的箱线图，真实数据点互相叠加，箱线图香色依据汽缸数变化
mtcars$cylinder <- as.factor(mtcars$cyl)
qplot(cylinder, mpg , data=mtcars ,geom = c("boxplot" , "jitter"),fill=cylinder)
#创建以车重为条件的没加仑英里数散点图矩阵，利用颜色和符号相撞区分自动挡和手动挡
#对每种传动类型添加各自的回归线和置信区间带
transmission <- factor( mtcars$am , levels = c(0,1) ,labels = c("Automatic","Manual"))
qplot(wt,mpg, data=mtcars,
      color=transmission, shape=transmission,
      geom=c("point", "smooth"),
      method="lm", formula=y~x,
      xlab="Weight", ylab="Miles Per Gallon",
      main="Regression Example")

#创建以栅栏图，每个面板展示车重与每加仑英里数的散点图
mtcars$cyl <- factor(mtcars$cyl,levels = c(4,6,8) , labels = c("4 cylinders","6 cylinders","8 cylinders"))
mtcars$am <- factor(mtcars$am,levels = c(0,1),labels = c("Automatic","Manual"))
qplot(wt,mpg,data = mtcars , facets  = am~cyl,size = hp)



library(ggplot2)
data(singer , package = "lattice")
qplot(height, data=singer, geom=c("density"),
      facets=voice.part~., fill=voice.part)


plot(mtcars$wt ,mtcars$mpg)
identify(mtcars$wt,mtcars$mpg,labels = row.names(mtcars))
library(playwith)
library(lattice)
playwith( xyplot(mpg~wt|factor(cyl)*factor(am) , data = mtcars ,subscripts = TRUE,type = c("r","p")))


