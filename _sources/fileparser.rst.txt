Parsing run history and savegame data
=====================================

.. module:: gamedata

.. class:: FileParser

    .. property:: timestamp

        Gives the UNIX timestamp of the run's end (or last save)

        :rtype: int

    .. property:: display_name

        A human-readable name of the run

        :rtype: str

    .. property:: character

        The character being played in this run

        :rtype: str

    .. property:: modded

        Whether the character is modded

        :rtype: bool

    .. property:: current_hp_counts

        The current HP in all the floors

        :rtype: list[int]

    .. property:: max_hp_counts

        The max HP in all the floors

        :rtype: list[int]

    .. property:: gold_counts

        The gold in all the floors

        :rtype: list[int]
