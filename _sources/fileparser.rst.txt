Parsing run history and savegame data
=====================================

.. module:: gamedata

.. class:: FileParser(data)

    The base parsing class for both past runs and ongoing ones.

    :param data: The JSON from either run files or savegame (determined by the subclass) to use for parsing.
    :type data: dict[str, Any]

    .. attribute:: neow_bonus

        All of the relevant data for the Neow bonus and floor 0.

        :type: :class:`NeowBonus`

    .. attribute:: done

        Whether the run is complete or in progress.

        :type: bool

    .. attribute:: prefix

        The prefix for some attributes that are shared between run history and savefile.

        :type: str

    .. property:: timestamp

        Give the UNIX timestamp of the run's end (or last save). This is implemented by the subclasses.

        :rtype: datetime.datetime

    .. property:: timedelta

        How much time has passed since the run has been played. This is implemented by the subclasses.

        :rtype: datetime.timedelta

    .. property:: profile

        The profile that the run was played on. This is implemented by the subclasses.

        :rtype: :class:`Profile`

    .. property:: character_streak

        The position of this run in the character streak. This is implemented by the subclasses.

        :rtype: :class:`runs.StreakInfo`

    .. property:: rotating_streak

        The position of this run in the rotating streak. This is implemented by the subclasses.

        :rtype: :class:`runs.StreakInfo`

    .. property:: display_name

        A human-readable name of the run. This is implemented by the subclasses.

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

        A list of all the cards that were removed this run. This is implemented by the subclasses.

        :rtype: list[str]

    .. method:: _get_removals()

        Internal method to get the card name and metadata for :meth:`removals_as_html` and :attr:`removals`. Should not be used by external code.

        :rtype: Generator[tuple[str, dict[str, str]], None, None]

    .. method:: master_deck_as_html()

        All of the current deck, properly formatted, with the small cards next to them, in three columns.

        :rtype: Generator[str, None, None]

    .. method:: removals_as_html()

        All of the removed cards, properly formatted, with the small cards next to them, in three columns.

        :rtype: Generator[str, None, None]

    .. method:: _cards_as_html(cards)

        Internal method to properly format an iterable of cards, called by :meth:`master_deck_as_html` and :meth:`removals_as_html`. Should not be used by external code.

        :param cards: An iterable of cards to format.
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

        Get the run's score. This is implemented by the subclasses.

        :rtype: int

    .. property:: score_breakdown

        Get the score breakdown of the run. This is implemented by the subclasses.

        :rtype: list[str]

    .. method:: get_floor(floor)

        Get the node corresponding to a given floor.

        :param int floor: The floor to get the data for
        :rtype: :class:`NodeData` or None

.. class:: runs.RunParser(filename, profile, data)

    The data for finalized runs. This is a :class:`FileParser` subclass.

    :param str filename: The on-disk name for the run file.
    :param int profile: The profile the run was played on.
    :param data: The decoded JSON from the run file. This is passed to :class:`FileParser`'s constructor.

    .. attribute:: filename

        The on-disk name for the run file.

        :type: str

    .. attribute:: name

        The name of the run to be used in the website URL. This is typically the filename without the extension.

        :type: str

    .. attribute:: matched

        The runs that are before and after this, based on the various criteria.

        :type: :class:`RunLinkedListNode`

    .. property:: won

        Whether the run won.

        :rtype: bool

    .. property:: verb

        Which verb to use for the run.

        :rtype: str

    .. property:: killed_by

        Which enemy encounter killed us, if relevant.

        :rtype: str or None

    .. property:: floor_reached

        The highest floor the run reached.

        :rtype: int

    .. property:: final_health

        The final health value of the run, in a (current, max) tuple.

        :rtype: tuple[int, int]

    .. property:: run_length

        How long the run lasted, in a human-readable format.

        :rtype: str

    .. method:: _get_streak(*, is_character_streak)

        Calculate and return the streak info for this run. This is used by :meth:`FileParser.character_streak` and :meth:`FileParser.rotating_streak`.

        :param bool is_character_streak: Whether to calculate the character or rotating streak.
        :rtype: :class:`runs.StreakInfo`

.. class:: runs.StreakInfo(streak, position, is_ongoing)

    The streak information for runs. This is a named tuple.

    :param int streak: The total streak count.
    :param int position: The position of the run in the streak.
    :param bool is_ongoing: Whether the streak is still going.

.. class:: save.Savefile

    The run information for the ongoing run. There can only ever be one instance of this during the lifetime of the process. The active instance can be obtained with :func:`save.get_savefile`. This is a :class:`FileParser` subclass.

    .. method:: update_data(data, character, has_run)

        Update the data for the current run with the new savefile.

        :param data: The decoded JSON received from the game's savefile.
        :type data: dict[str, Any] or None
        :param str character: The character being played for this run.
        :param str has_run: A stringified boolean value of whether there is a matching run file corresponding to this run. This will only be checked if `data` is None.

    .. property:: in_game

        Whether there is currently a run going on.

        :rtype: bool

    .. property:: current_health

        The run's current health.

        :rtype: int

    .. property:: max_health

        The run's max health.

        :rtype: int

    .. property:: current_gold

        The run's current gold count.

        :rtype: int

    .. property:: current_purge

        The current cost of the merchant's card removal service.

        :rtype: int

    .. property:: purge_totals

        How many cards were removed using the merchant's card removal service.

        :rtype: int

    .. property:: shop_prices

        The current shop prices. This is a tuple of 4 tuples; (cards, colorless, relics, potions). Each tuple is a tuple of range objects for the prices of the (common, uncommon, rare) rarity for each. The colorless tuple is only (uncommon, rare). The items can cost as low as `obj.start` and as high as `obj.stop`, slightly breaking Pythonic assumptions. This is fine.

        :rtype: tuple[tuple[range, range, range], tuple[range, range], tuple[range, range, range], tuple[range, range, range]]

    .. property:: current_floor

        The current floor of the run.

        :rtype: int

    .. property:: potion_chance

        The current chance for a potion to drop after a fight. This is a percentage.

        :rtype: int

    .. property:: rare_chance

        The current chance to see any rare card after. This is a tuple of (fight rewards, elite rewards, shop contents), indicating the odds for that particular point to have a rare card. This is a chance to see *any* rare card, not just one. This is a percentage.

        :rtype: tuple[float, float, float]

    .. method:: rare_chance_as_str()

        Same as the above, except as human-readable strings.

        :rtype: tuple[str, str, str]

    .. property:: upcoming_boss

        Which is the upcoming act boss. This only returns the first Act 3 boss, if relevant.

        :rtype: str

    .. property:: bottles

        The bottle relics and their bottled cards.

        :rtype: list[:class:`BottleRelic`]

    .. method:: _get_card_string(card, upgrades)

        Get the canonical name of the card. Used by :attr:`bottles`.

        :staticmethod:
        :param str card: The internal name of the card.
        :param int upgrades: The number of upgrades on the card.
        :return: The canonical name of the card.
        :rtype: str

    .. method:: _get_score_bonuses()

        Get the score bonuses for :attr:`score_breakdown`.

        :rtype: list[:class:`score.Score`]

    .. property:: monsters_killed

        How many regular enemies were killed.

        :rtype: int

    .. property:: act1_elites_killed

        How many Exordium elites were killed.

        :rtype: int

    .. property:: act2_elites_killed

        How many City elites were killed.

        :rtype: int

    .. property:: act3_elites_killed

        How many Beyond elites were killed.

        :rtype: int

    .. property:: perfect_elites

        How many elites were killed without taking damage.

        :rtype: int

    .. property:: perfect_bosses

        How many bosses were killed without taking damage.

        :rtype: int

    .. property:: has_overkill

        Whether we did 99 or more damage with a single attack.

        :rtype: bool

    .. property:: mystery_machine_counter

        How many unknown rooms were visited.

        :rtype: int

    .. property:: total_gold_gained

        How much gold has been acquired in the entire run.

        :rtype: int

    .. property:: has_combo

        Whether we played 20 or more cards in a single turn.

        :rtype: bool

    .. property:: act_num

        The act we are currently in.

        :rtype: int

    .. property:: deck_card_ids

        The internal card names, for score breakdown purposes.

        :rtype: list[str]

.. function:: save.get_savefile([context=None])
    :async:

    Get the current savefile, or None if no run is ongoing.

    :param context: The message context that triggered the call, or None.
    :type context: A TwitchIO or DiscordPY message context, or None
    :return: The current savefile or None
    :rtype: :class:`save.Savefile` or None

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

        :param obtained: The node this relic was obtained.
        :type obtained: :class:`NodeData`
        :param last: The last node with this relic (aka current node).
        :type last: :class:`NodeData`
        :rtype: list[str]

    .. property:: image

        The image name for the relic, as present in the static/relics directory.

        :rtype: str

    .. property:: name

        The in-game name of the relic.

        :rtype: str
