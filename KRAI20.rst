######################################################################################################################################################
KRAI20. Закупающая организация провела закупку методом прямого заключения по причине "возникновения срочной необходимости в проведении закупок ежедневно потребляемых товаров в связи с обстоятельствами, которые не предвидела закупающая организация"   но обязательный конкурс в течение месяца со дня возникновения указанных обстоятельств проведен не был. 
######################################################################################################################################################

***************
Суть индикатора
***************

Индикатор отслеживает случаи, когда закупающая организация проводит процедуру прямого заключения договора по причине "возникновения срочной необходимости в проведении закупок ежедневно потребляемых товаров в связи с обстоятельствами, которые не предвидела закупающая организация"   но обязательный конкурс в течение месяца со дня возникновения указанных обстоятельств проведен не был.

****
Риск
****

Избегание применения конкурсных процедур с целью заключения договора с "удобным" поставщиком. 


*******************************
Нарушение норм/принципов закона
*******************************

Статья 21. Закупки методом прямого заключения договора. 4. Закупающая организация вправе самостоятельно провести закупки методом прямого заключения договора в случаях: 8) возникновения срочной необходимости в проведении закупок ежедневно потребляемых товаров в связи с обстоятельствами, которые не предвидела закупающая организация, при условии обязательного проведения конкурса в течение месяца со дня возникновения указанных обстоятельств. 

***********************************
Основание для разработки индикатора
***********************************

Индикатор вводится, так как в системе не реализован контроль проведения конкурса в течение месяца после проведения процедуры методом прямого заключения договора в случае "возникновения срочной необходимости в проведении закупок ежедневно потребляемых товаров в связи с обстоятельствами, которые не предвидела закупающая организация, при условии обязательного проведения конкурса в течение месяца со дня возникновения указанных обстоятельств".

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
- Транзакционные переменные :ref:`tv_tenderCPVList`, :ref:`tv_badDataQuality`
- Аналитическая таблица :ref:`tbl_PEOpenTenders`

Типы процедур
=============

Индикатор рассчитывается для следующих типов процедур:

- метод прямого заключения договора.


Статусы процедур
----------------

Индикатор рассчитывается для процедур, которые:

- находятся в статусе``complete`` более 30 дней (количество дней от текущего момента до ``data.tender.date``).


Частота расчета
===============

Если выполнены все условия для активации расчета индикатор, он рассчитывается один раз.

Качество данных
===============

Если значение переменной :ref:`tv_badDataQuality` для данной процедуры равно ``true``, индикатор принимает значение ``-1`` и расчет его не производится.

Поля для расчета
================

Для расчета индикатора используются следующие поля API модуля системы гос. закупок:

- ``data.tender.procurementMethodRationale``
- ``data.tender.date``
- ``data.parties.roles``
- ``data.parties.identifier.scheme``
- ``data.parties.identifier.id``

Формула расчета
===============

1. Выбираем только процедуры, у которых ``data.tender.procurementMethodRationale = 'urgentNeed'``.

2. Определяем идентификатор закупающей организации (``data.parties.identifier.id``) элемента ``data.parties``, у которого ``data.parties.roles = 'buyer, procuringEntity'``.

3. Определяем дату завершения процедуры ``data.tender.date``.

4. Из аналитической таблицы выбираем все строки для нашей закупающей организации, где дата позднее, но не более чем на 30 дней, чем дата завершения из пункта 3.

5. Сравниваем есть ли элементы :ref:`tenderCPVList` нашей процедуры в отфильтрованных строках в таблице.

6. Если все элементы есть в строках, индикатор принимает значение ``0``. В противном случае, индикатор принимает значение ``1``. 


Факторы, которые влияют на корректное срабатывание индикатора
=============================================================

Индикатор может срабатывать неправильно, если закупающая организация не отображает на портале все фактически пройденные этапы процедуры закупки.
