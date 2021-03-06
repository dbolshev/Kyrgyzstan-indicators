######################################################################################################################################################
KRAI6. Закупка работ и услуг методом прямого заключения договора превышает 25% стоимости договора, заключенного на основании проведенного конкурса при сохранении цены и технических спецификаций. 
######################################################################################################################################################

***************
Суть индикатора
***************

Индикатор отслеживает случаи, когда закупающая организация закупает работы или услуги методом прямого заключения договора, используя обоснование "осуществления дополнительных строительных работ или услуг, не превышающих 25 процентов от стоимости заключенного договора на основании конкурса, связанных с расширением ранее начатых работ либо увеличением объема, а привлечение того же подрядчика обеспечит экономичность и совместимость результатов в отношении качества ранее выполненных работ или услуг".

****
Риск
****

Избегание применения конкурсных процедур с целью заключения договора с "удобным" поставщиком. 

*******************************
Нарушение норм/принципов закона
*******************************

Статья 21. П.4. Закупающая организация вправе самостоятельно провести закупки методом прямого заключения договора в случаях: 2) осуществления дополнительных строительных работ или услуг, не превышающих 25 процентов от стоимости заключенного договора на основании конкурса, связанных с расширением ранее начатых работ либо увеличением объема, а привлечение того же подрядчика обеспечит экономичность и совместимость результатов в отношении качества ранее выполненных работ или услуг.

***********************************
Основание для разработки индикатора
***********************************

Индикатор вводится, так как в системе не реализован контроль соответствия цены, технических требований и соотношений сумм вышеописанных причин.

***************************
Индикатор прекращает работу
***************************
Индикатор прекращает работу 28.06.2019 в связи со вступлением в действие измененного Закона КР о госзакупках.


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

Типы процедур
=============

Индикатор рассчитывается для следующих типов процедур:

- метод прямого заключения договора.


Статусы процедур
----------------

Индикатор рассчитывается для процедур, которые:

- находятся в статусе ``complete``


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
- ``data.relatedProcesses.relationship``
- ``data.tender.procurementMethodDetails``
- ``data.relatedProcesses.identifier``
- ``data.tender.items.classification.scheme``
- ``data.tender.items.classification.id``
- ``data.item.relatedLot``
- ``data.awards.relatedLot``
- ``data.awards.relatedBid``
- ``data.bids.details.priceProposal.unit.value.amount``
- ``data.bids.relatedLots.value.amount``

Для расчета используются следующие транзакционные переменные:

- :ref:`tv_tenderCPVList`

Формула расчета
===============

1. Выбираем только процедуры, у которых ``data.tender.procurementMethodRationale = 'additionalProcurement25'``.

2. Если в процедуре отсутствует контейнер ``data.relatedProcesses``, индикатор принимает значение ``-1``. Расчет заканчивается.

3. Выбираем предыдущую процедуру открытых торгов: такой номер ``data.relatedProcesses.identifier``, которому соответствует ``data.relatedProcesses.relationship = 'prior'``.

4. Если у найденной процедуры ``data.tender.procurementMethodDetails``не равно ``oneStage``, ``downgrade`` или ``simplicated``, индикатор принимает значение ``-1``. Расчет заканчивается.

5. Если статус найденной процедуры ``data.tender.status != 'contractSigned'``, индикатор принимает значение ``-1``. Расчет заканчивается.

6. Все элементы списка переменной :ref:`tv_tenderCPVList` должны находиться в соответствующей переменной найденной процедуры. Иначе, индикатор принимает значение ``-1``. Расчет заканчивается.

7. Для каждого предмета закупки проводим следующие действия.
    - В исследуемой процедуре находим элемент ``data.items``, в котором ``data.tender.items.classification.id`` равен нашему.
    - Определяем идентификатор лота ``data.items.relatedLot``, к которому относится найденный ``data.items``.
    - Находим блок определения победителя, где ``data.awards.relatedLot = data.items.relatedLot`` и ``data.awards.status = 'active'``.
    - В найденном блоке определения победителя находим идентификатор победившего предложения ``data.awards.relatedBid``.
    - По найденному идентификатору находим выигравшее предложение ``data.awards.relatedBid = data.bids.details.id``.
    - В выигравшем предложении в блоке ``data.bids.priceProposal`` находим цену единицы измерения предмета закупки ``data.bids.details.priceProposal.unit.value.amount``.
    - По такой же схеме находим стоимость исследуемого предмета закупки в предыдущей процедуре открытых торгов.
    - Если найденные цены единиц измерения отличаются, индикатор принимает значение ``-1``. Расчет заканчивается.

8. В найденных выигравших предложения из предыдущего шага сравниваем суммы сумм выигравших предложений ``data.bids.relatedLots.value.amount``. Если сумма исследуемой процедуры составляет больше 25% от суммы предшествующей конкурентной процедуры, индикатор принимает значение ``1``. Расчет заканчивается.

9. Если мы дошли до этого пункта, индикатор принимает значение ``0``.

Факторы, которые влияют на корректное срабатывание индикатора
=============================================================

Индикатор может срабатывать неправильно, если код предмета закупки, указанный закупающей организацией не детализирован достаточно для точной идентификации предмета закупки.
