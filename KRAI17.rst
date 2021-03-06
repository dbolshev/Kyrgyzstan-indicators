######################################################################################################################################################
KRAI17. Сумма всех заключенных контрактов по лотам в закупке превышает сумму всех выигравших конкурсных заявок. 
######################################################################################################################################################

***************
Суть индикатора
***************

Индикатор отслеживает случаи, когда закупающая организация подписывает контракты на сумму большую, чем сумма выигрышных предложений в процедуре.

****
Риск
****

Манипуляции с выигрышными заявками и стоимостью в контракте.


*******************************
Нарушение норм/принципов закона
*******************************

Статья 32. Публичное объявление итогов конкурса и заключение договора. 3. С поставщиком (подрядчиком), конкурсная заявка которого была признана выигравшей, заключается договор государственных закупок в соответствии с условиями конкурсной заявки (в срок действия конкурсной заявки) с учетом требований, установленных в части 2 настоящей статьи.

***********************************
Основание для разработки индикатора
***********************************

Индикатор вводится, так как в системе не реализован контроль соответствия сумм в контрактах суммам выигравших предложений.

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

- одноэтапный метод;
- двухэтапный метод;
- метод на понижение цены;
- упрощенный метод;
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

- ``data.contracts.value.amount``
- ``data.contracts.status``
- ``data.awards.value.amount``
- ``data.awards.status``


Формула расчета
===============

1. Выбираем все активные элементы определения победителя ``data.awards``, у которых ``data.awards.status = 'active'``.

2. Из этих элементов суммируем все найденные суммы ``data.awards.value.amount``.

3. Выбираем все активные элементы контрактов ``data.contracts``, у которых ``data.contracts.status = 'active'``.

4. Из этих элементов суммируем все найденные суммы ``data.contracts.value.amount``.

5. Если суммы из пункта 2 меньше суммы из пункта 4, индикатор принимает значение ``1``. В противном случае индикатор принимает значение ``0``.


Факторы, которые влияют на корректное срабатывание индикатора
=============================================================

Индикатор может срабатывать неправильно, если код предмета закупки, указанный закупающей организацией не детализирован достаточно для точной идентификации предмета закупки.
