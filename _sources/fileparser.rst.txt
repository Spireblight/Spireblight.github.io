Parsing run history and savegame data
=====================================

.. module:: gamedata

.. class:: FileParser

    The base parsing class for both past runs and ongoing ones.

    .. attribute:: neow_bonus

        All of the relevant data for the Neow bonus and floor 0.

        :type: :class:`NeowBonus`

    .. property:: timestamp

        Give the UNIX timestamp of the run's end (or last save).

        :rtype: int

    .. property:: display_name

        A human-readable name of the run.

        :rtype: str

    .. property:: character

        The character being played in this run.

        :rtype: str

    .. property:: modded

        Whether the character is modded.

        :rtype: bool

    .. property:: current_hp_counts

        The current HP in all the floors.

        :rtype: list[int]

    .. property:: max_hp_counts

        The max HP in all the floors.

        :rtype: list[int]

    .. property:: gold_counts

        The gold in all the floors.

        :rtype: list[int]

    .. property:: ascension_level

        The ascension level of the run.

        :rtype: int

    .. property:: playtime

        The playtime as stored in the savefile.

        :rtype: int

    .. property:: keys

        The keys (if obtained) and the floor they were obtained on.

        :rtype: Generator[tuple[str, str | int], None, None]

    .. method:: _get_cards()

        Internal method to get the card name and metadata for :meth:`master_deck_as_html` and :attr:`cards`. Should not be used by external code.

        :rtype: Generator[tuple[str, dict[str, str]], None, None]

    .. property:: removals

        A list of all the cards that were removed this run.

        :abstractmethod:
        :rtype: list[str]

    .. method:: _get_removals()

        Internal method to get the card name and metadata for :meth:`removals_as_html` and :attr:`removals`. Should not be used by external code.

    .. method:: master_deck_as_html()

        All of the current deck, properly formatted, with the small cards next to them, in three columns.

        :rtype: Generator[str, None, None]

    .. method:: removals_as_html()

        All of the removed cards, properly formatted, with the small cards next to them, in three columns.

        :rtype: Generator[str, None, None]

    .. method:: _cards_as_html(cards)

        Internal method to properly format an iterable of cards, called by :meth:`master_deck_as_html` and :meth:`removals_as_html`. Should not be used by external code.

        :param cards: An iterable of cards to format
        :type cards: Iterable[tuple[str, dict[str, str]]]
        :rtype: Generator[str, None, None]

    .. property:: cards

        Get all of the cards currently part of the deck.

        :rtype: Generator[str, None, None]

    .. property:: relics

        List all of the relics the run currently has.

        :rtype: list[:class:`RelicData`]

    .. property:: seed

        The run's seed. This calculates it from the integer saved in the JSON file, then caches it.

        :return: The seed like Spire displays it
        :rtype: str

    .. property:: is_seeded

        Whether the run is seeded.

        :rtype: bool

    .. property:: path

        List the path that the run took, with proper info accompanying it.

        :rtype: list[:class:`NodeData`]

    .. property:: modifiers

        Show all modifiers the run has.

        :rtype: list[str]

    .. property:: modifiers_with_desc

        Return a human-friendly output of the modifiers with a short description.

        :rtype: list[str]

    .. property:: score

        Get the run's score.

        :abstractmethod:
        :rtype: int

    .. property:: score_breakdown

        Get the score breakdown of the run.

        :abstractmethod:
        :rtype: list[str]

    .. method:: get_floor(floor)

        Get the node corresponding to a given floor.

        :param int floor: The floor to get the data for
        :rtype: :class:`NodeData` or None

.. class:: RelicData(parser, relic)

    The class storing relic information and stats for displaying.

    :param parser: The parser being used to get the data from this.
    :type parser: :class:`FileParser`
    :param str relic: The internal name of the relic.

    .. method:: description()

        The floor the relic was obtained, as well as stats, using :meth:`get_details`.

        :rtype: str

    .. method:: escaped_description()

        Same as :meth:`description`, but properly escaped for HTML presentation.

        :rtype: str

    .. method:: get_details(obtained, last)

        Get the stats for the relic, if applicable.

        :param obtained: The node this relic was obtained
        :type obtained: :class:`NodeData`
        :param last: The last node with this relic (aka current node)
        :type last: :class:`NodeData`
        :rtype: list[str]

    .. property:: image

        The image name for the relic, as present in the static/relics directory.

        :rtype: str

    .. property:: name

        The in-game name of the relic.

        :rtype: str
