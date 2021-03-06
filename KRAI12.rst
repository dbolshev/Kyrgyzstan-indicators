######################################################################################################################################################
KRAI12. Закупающая организация недостаточно подробно описала предмет закупок технические, качественные характеристики закупаемых товаров, работ или услуг (отсутствие дополнительной документации включая технические спецификации, проектно-сметную документацию на весь объем строительства, государственные экспертизы по проектно-техническим решениям, планы, чертежи и эскизы). 
######################################################################################################################################################

***************
Суть индикатора
***************

Индикатор отслеживает случаи, когда закупающая организация не загружает дополнительные данные о предметах закупки при проведении закупки одноэтапным методом.

****
Риск
****

Неэффективная организация закупки, неполное описание предмета закупки (по причине недостатка компетенции либо манипуляции для осуществления закупки со ""своим"" поставщиком).  


*******************************
Нарушение норм/принципов закона
*******************************

"Статья 14. Разработка конкурсных документов. 
п. 2. Конкурсная документация в обязательном порядке содержит следующие сведения: 4) подробное описание предмета закупок, технические, качественные характеристики закупаемых товаров, работ или услуг, включая технические спецификации, проектно-сметную документацию на весь объем строительства, государственные экспертизы по проектно-техническим решениям, планы, чертежи и эскизы; количество товара; услуги, которые должны быть предоставлены; место, где должны быть поставлены товары, выполнены работы или оказаны услуги. Статья 23. Конкурсный период. Предоставление, разъяснение и изменение конкурсной документации. п. 1. Закупающая организация обязана разместить на веб-портале государственных закупок разработанный пакет конкурсных документов вместе с объявлением о конкурсе."

***********************************
Основание для разработки индикатора
***********************************

Индикатор вводится, так как в системе не реализован контроль наличия дополнительных требований к предметам закупки.

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
- Транзакционная переменная :ref:`tv_badDataQuality`

Типы процедур
=============

Индикатор рассчитывается для следующих типов процедур:

- одноэтапный метод.


Статусы процедур
----------------

Индикатор расчитывается для процедур, которые:

- находятся в статусе ``complete``
- находятся в статусе ``active`` c ``currentStage = 'evaluationComplete'`` более 30 дней (количество дней вычисляется, как количество дней от текущей даты до самой ранней даты из объектов ``data.awards``).


Частота расчета
===============

Если выполнены все условия для активации расчета индикатор, он рассчитывается один раз.

Качество данных
===============

Если значение переменной :ref:`tv_badDataQuality` для данной процедуры равно ``true``, индикатор принимает значение ``-1`` и расчет его не производится.

Поля для расчета
================

Для расчета индикатора используются следующие поля API модуля системы гос. закупок:

- ``data.documents``


Формула расчета
===============

1. Если в процедуре отсутствует блок ``data.documents``, индикатор принимает значение ``1``, в противном случае индикатор принимает значение ``0``.

Факторы, которые влияют на корректное срабатывание индикатора
=============================================================

Индикатор может срабатывать неправильно, если код предмета закупки, указанный закупающей организацией не детализирован достаточно для точной идентификации предмета закупки.
