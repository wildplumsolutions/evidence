---
title: Example Evidence App using Titanic data
---

_https://www.kaggle.com/datasets/ibrahimelsayed182/titanic-dataset_

This demo [connects](/settings) to a local CSV file.

<BigValue data={t_embarked} value=total_number_of_passengers/>

### Passenger Port of Embarkation

<BarChart 
  data={t_embarked} 
  x=PortofEmbarkation
  y=number_of_passengers 
  fillColor="#488f96"
  sort=false
>
</BarChart>

<Dropdown data={t_embarked} name=ports value=PortofEmbarkation>
    <DropdownOption value="All Ports" valueLabel="All Ports"/>
</Dropdown>

### Passenger Breakdown

Total Passengers embarked {inputs.ports.value} are **<Value data={t_class} column=total_number_of_passengers/>**.

<BarChart 
  data={t_class} 
  x=PassengerClass
  y=number_of_passengers 
  fillColor="#488f96"
  sort=false
  title="Passenger Class - {inputs.ports.value}"
>
</BarChart>

<BarChart 
  data={t_passenger} 
  x=passenger
  y=number_of_passengers 
  fillColor="#488f96"
  title="Passenger - {inputs.ports.value}"
>
</BarChart>

### Survival Breakdown

Total Passengers survived {inputs.ports.value} are **<Value data={t_class} column=total_number_of_passengers/>**.

<BarChart 
  data={t_survived} 
  x=survived
  y=number_of_passengers 
  fillColor="#488f96"
  sort=false
  title="Passengers Survived from - {inputs.ports.value}"
>
</BarChart>

<Heatmap 
    data={t_survived_age} 
    x=survived 
    y=age 
    value=number_of_passengers 
    valueFmt="#"
    title="Passengers Age from - {inputs.ports.value}"
/>

```sql t_embarked
select
  case when embarked like 'C' then 'Cherbourg'
      when embarked like 'Q' then 'Queenstown'
      else 'Southampton' end as 'PortofEmbarkation'
  , count(*) as number_of_passengers
  , SUM(COUNT(*)) OVER() AS total_number_of_passengers
from titanic
group by 1 
order by 1 ASC
```

```sql t_class
select
  case when class like 'First' then '1st'
      when class like 'Second' then '2nd'
      else '3rd' end as 'PassengerClass'
  , count(*) as number_of_passengers
  , SUM(COUNT(*)) OVER() AS total_number_of_passengers
from titanic
where case when '${inputs.ports.value}' like 'All Ports' then 'Y'
    else case when case when embarked like 'C' then 'Cherbourg'
                        when embarked like 'Q' then 'Queenstown'
                        else 'Southampton' end like '${inputs.ports.value}' then 'Y'
          else 'N' end
    end = 'Y'
group by 1 
order by 1 ASC
```

```sql t_passenger
select
  who as passenger,
  count(*) as number_of_passengers
from titanic
where case when '${inputs.ports.value}' like 'All Ports' then 'Y'
    else case when case when embarked like 'C' then 'Cherbourg'
                        when embarked like 'Q' then 'Queenstown'
                        else 'Southampton' end like '${inputs.ports.value}' then 'Y'
          else 'N' end
    end = 'Y'
group by 1 
order by 1 desc
```

```sql t_survived
select
  case when survived = 1 then 'Yes' else 'No' end as survived
  , count(*) as number_of_passengers
from titanic
where case when '${inputs.ports.value}' like 'All Ports' then 'Y'
    else case when case when embarked like 'C' then 'Cherbourg'
                        when embarked like 'Q' then 'Queenstown'
                        else 'Southampton' end like '${inputs.ports.value}' then 'Y'
          else 'N' end
    end = 'Y'
group by 1 
order by 1 desc
```

```sql t_survived_age
select
  case when age between 0 and 10 then '0-10'
      when age between 11 and 20 then '11-20'
      when age between 21 and 30 then '21-30'
      when age between 31 and 40 then '31-40'
      when age between 41 and 50 then '41-50'
      when age between 51 and 60 then '51-60'
      when age between 61 and 70 then '61-70'
      when age between 71 and 80 then '71-80'
      when age between 81 and 90 then '81-90'
      when age between 91 and 100 then '91-100'
      when age > 100 then '100+'
      else 'Error'
      end as age
  , case when survived = 1 then 'Yes' else 'No' end as survived
  , count(*) as number_of_passengers
from titanic
where case when '${inputs.ports.value}' like 'All Ports' then 'Y'
    else case when case when embarked like 'C' then 'Cherbourg'
                        when embarked like 'Q' then 'Queenstown'
                        else 'Southampton' end like '${inputs.ports.value}' then 'Y'
          else 'N' end
    end = 'Y'
group by 1, 2
order by 1 desc
```