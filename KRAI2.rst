######################################################################################################################################################
KRAI2. Дробление. Заключение прямого договора с разными причинами (одно и то же наименование ОКГЗ но указывает разные причины применения закупки методом прямого заключения договора).
######################################################################################################################################################

***************
Суть индикатора
***************

Индикатор отслеживает случаи, когда закупающая организация закупает один и тот же предмет закупки, используя процедуру прямого заключения договора с разными обоснованиями.

****
Риск
****

Избегание применения конкурсных процедур с целью заключения договора с "удобным" поставщиком.  

*******************************
Нарушение норм/принципов закона
*******************************

Статья 21. П.4. Закупающая организация вправе самостоятельно провести закупки методом прямого заключения договора в случаях приобретения товаров, работ и услуг по каждой статье расходов один раз в год до минимальной пороговой суммы.

***********************************
Основание для разработки индикатора
***********************************

Индикатор вводится, так как в системе не реализован контроль количества закупок закупающей организации методом прямого заключения договора с одинаковым предметом закупки.

******************************
Методология расчета индикатора
******************************

Уровень расчета
===============
Индикатор рассчитывается на уровне *процедуры*.

Источники данных для расчета
============================

Для расчета индикатора используются следующие источники данных:

- API системы государственных закупок в OCDS формате
- Аналитическая таблица :ref:`tbl_reportCPV`
- Транзакционные переменные :ref:`tv_tenderCPVList`, :ref:`tv_badDataQuality`

Типы процедур
=============

Индикатор рассчитывается для следующих типов процедур:

- метод прямого заключения договора.


Статусы процедур
----------------

Индикатор рассчитывается для процедур, которые:

- находятся статусе ``complete``


Частота расчета
===============

Если выполнены все условия для активации расчета индикатор, он рассчитывается один раз.

Качество данных
===============

Если значение переменной :ref:`tv_badDataQuality` для данной процедуры равно ``true``, индикатор принимает значение ``-1`` и расчет его не производится.

Поля для расчета
================

Для расчета индикатора используются следующие поля API модуля системы гос. закупок:

- ``data.tender.status``
- ``data.tender.items.classification.id``
- ``data.tender.items.relatedLot``
- ``data.tender.lots.status``
- ``parties.id``
- ``parties.roles``
- ``data.tender.datePublished``
- ``data.tender.date``

Для расчета используются следующие транзакционные переменные:

- :ref:`tv_tenderCPVList`

Для расчета используются следующие аналитические таблицы:

- :ref:`tbl_reportCPV`

Формула расчета
===============

1. Выбираем только процедуры, которые оглашены в текущем году.

2. Выбираем все значения классификаторов из текущей процедуры (``data.tender.items.classification.id``) только из тех лотов (``data.tender.items.relatedLot = data.tender.lots.id``), у которых ``data.tender.lots.status = 'complete'`` или ``data.tender.lots.status = 'active'``.

3. Выбираем идентификатор закупающей организации:  ``parties.id``, для которой ``parties.roles = 'buyer, procuringEntity'``.

4. По идентификатору закупающей организации в аналитической таблице находим список всех предметов закупок, которые уже закупались ранее (в поле ``data.tender.date`` текущей процедуры стоит более поздняя дата, чем дата в строке таблицы) в этом году прямым заключением договора.

5. Если хоть один предмет закупки из текущей процедуры находится в списке в аналитической таблице, индикатор принимает значение "1". В противном случае индикатор принимает значение "0".

Факторы, которые влияют на корректное срабатывание индикатора
=============================================================

Индикатор может срабатывать неправильно, если код предмета закупки, указанный закупающей организацией не детализирован достаточно для точной идентификации предмета закупки.
