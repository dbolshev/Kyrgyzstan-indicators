######################################################################################################################################################
KRAI11. Закупающая организация проводит закупку с указанием цены за единицу товара которая сильно (более 20%) отличается от средней цены за аналогичный товар по системе. 
######################################################################################################################################################

***************
Суть индикатора
***************

Индикатор отслеживает случаи, когда закупающая организация закупает работы или услуги методом прямого заключения договора, но цена закупки сильно отличается от средних цен по аналогичным закупкам других закупающих организаций.
****
Риск
****

Неэффективное планирование и использование бюджетных средств. 
Предоставление завышенной цены "своим" поставщикам с целью получения вознаграждения от поставщика, "откат".

*******************************
Нарушение норм/принципов закона
*******************************

Статья 1. Цель и принципы Закона. Целью настоящего Закона является обеспечение экономичности и эффективности использования государственных средств при осуществлении государственных закупок. 

***********************************
Основание для разработки индикатора
***********************************

Индикатор вводится, так как в системе не реализован контроль соответствия цен рыночным.

******************************
Методология расчета индикатора
******************************

Уровень расчета
===============
Индикатор рассчитывается на уровне *лота*.

Источники данных для расчета
============================

Для расчета индикатора используются следующие источники данных:

- API системы государственных закупок в OCDS формате
- Аналитическая таблица :ref:`tbl_CPVMeanPrice`
- Транзакционная переменная :ref:`tv_badDataQuality`

Типы процедур
=============

Индикатор рассчитывается для следующих типов процедур:

- метод прямого заключения договора.


Статусы процедур
----------------

Индикатор рассчитывается для процедур, которые:

- находятся в статусе ``complete``.


Частота расчета
===============

Если выполнены все условия для активации расчета индикатор, он рассчитывается один раз.

Качество данных
===============

Если значение переменной :ref:`tv_badDataQuality` для данной процедуры равно ``true``, индикатор принимает значение ``-1`` и расчет его не производится.

Поля для расчета
================

Для расчета индикатора используются следующие поля API модуля системы гос. закупок:

- ``data.tender.items.classification.scheme``
- ``data.tender.items.classification.id``
- ``data.tenders.datePublished``
- ``data.items.id``
- ``data.items.unit.id``
- ``data.item.relatedLot``
- ``data.awards.relatedLot``
- ``data.awards.relatedBid``
- ``data.bids.priceProposal.relatedItem``
- ``data.bids.details.priceProposal.unit.value.amount``
- ``data.bids.relatedLots.value.amount``


Формула расчета
===============

1. Из процедуры извлекаем идентификаторы предметов закупки (``data.tender.items.classification.id``) и единицы измерения ``data.items.unit.id``. Предметы закупки выбираем только из таких лотов (``data.item.relatedLot = data.tender.lots.id``), которые имеют ``data.tender.lots.status = 'complete'`` или ``data.tender.lots.status = 'active'``.

2. Для каждой найденной пары ``data.tender.items.classification.id`` и ``data.items.unit.id`` проводим следующие действия:
    - Определяем идентификатор лота ``data.items.relatedLot``, к которому относится найденный ``data.items``.
    - Находим блок определения победителя, где ``data.awards.relatedLot = data.item.relatedLot`` и ``data.awards.status = 'active'``.
    - В найденном блоке определения победителя находим идентификатор победившего предложения ``data.awards.relatedBid``.
    - По найденному идентификатору находим выигравшее предложение ``data.awards.relatedBid = data.bids.details.id``.
    - В выигравшем предложении в блоке ``data.bids.priceProposal`` в блоке для нашего элемента (``data.bids.priceProposal.relatedItem = data.items.id``) находим цену единицы измерения предмета закупки ``data.bids.details.priceProposal.unit.value.amount``.
    - Сравниваем найденную цену с ценой из аналитической таблицы для нашей единицы измерения (из аналитической таблицы выбираем только те строки, год в которых соответствует году публикации процедуры).
    - Если в аналитической таблице для данного кода предмета закупки и единицы измерения отсутствует строка по стоимости единицы измерения, индикатор принимает значение ``0`` для ``data.lots.id = data.item.relatedLot``, расчет заканчивается.  
    - Если в аналитической таблице для данного кода предмета закупки и единицы измерения присутствует строка со средним значением, вычисляем сколько процентов составляет цена из процедуры от средней цены в аналитической таблице.
    - Если найденные цены отличаются больше, чем на 20% в любую сторону, индикатор принимает значение ``1`` для ``data.lots.id = data.item.relatedLot``. Расчет заканчивается.

3. Если мы дошли до этого пункта, индикатор принимает значение ``0`` для ``data.lots.id = data.item.relatedLot``.

Факторы, которые влияют на корректное срабатывание индикатора
=============================================================

Индикатор может срабатывать неправильно, если код предмета закупки, указанный закупающей организацией не детализирован достаточно для точной идентификации предмета закупки.
