# Coronavirus

devtools::install_github("RamiKrispin/coronavirus")
install.packages('gganimate', dependencies = T)

library(coronavirus)
library(sf)
library(tidyverse)
library(lubridate)
library(scico)
library(ggmap)
library(gganimate)
library(scales)

head(coronavirus)
coro <- coronavirus
coro$date <- lubridate::as_date(coro$date, locale='es')

coro <- coro %>% 
  group_by(Country.Region, type) %>% 
  mutate(acc=cumsum(cases)) %>% 
  ungroup() %>% 
  filter(cases>2) %>% 
  mutate(type=str_to_sentence(type))
  

p <- ggplot(coro, aes(date, acc , shape=type, color=type, group=type)) +
  geom_line() +
  geom_point()+
  scale_y_continuous(labels=comma)+
  scale_color_scico_d(palette = 'berlin') +
  theme_classic(base_size = 13) +
  labs(x=NULL, y='Cases', color=NULL, shape=NULL, 
       caption='https://github.com/ACalleros/')+
  facet_wrap(~Country.Region, scales = 'free') +
  theme(axis.text.x = element_text(angle=45, vjust = 1))+
  transition_reveal(date) 

a <- animate(p, renderer = av_renderer(), width=3200, height =3200, res=250)

anim_save('cov19.mpg', animation=a)
