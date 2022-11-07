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

        :type: datetime.datetime

    .. property:: timedelta

        How much time has passed since the run has been played. This is implemented by the subclasses.

        :type: datetime.timedelta

    .. property:: profile

        The profile that the run was played on. This is implemented by the subclasses.

        :type: :class:`Profile`

    .. property:: character_streak

        The position of this run in the character streak. This is implemented by the subclasses.

        :type: :class:`runs.StreakInfo`

    .. property:: rotating_streak

        The position of this run in the rotating streak. This is implemented by the subclasses.

        :type: :class:`runs.StreakInfo`

    .. property:: display_name

        A human-readable name of the run. This is implemented by the subclasses.

        :type: str

    .. property:: character

        The character being played in this run.

        :type: str

    .. property:: modded

        Whether the character is modded.

        :type: bool

    .. property:: current_hp_counts

        The current HP in all the floors.

        :type: list[int]

    .. property:: max_hp_counts

        The max HP in all the floors.

        :type: list[int]

    .. property:: gold_counts

        The gold in all the floors.

        :type: list[int]

    .. property:: ascension_level

        The ascension level of the run.

        :type: int

    .. property:: playtime

        The playtime as stored in the savefile.

        :type: int

    .. property:: keys

        The keys (if obtained) and the floor they were obtained on. This is implemented by the subclasses.

        :type: :class:`KeysObtained`

    .. property:: _master_deck

        The main deck, with the internal names. This is implemented by the subclasses.

        :type: list[str]

    .. method:: get_cards()

        Generator to get the cards in the main deck.

        :rtype: Generator[:class:`CardData`, None, None]

    .. method:: get_meta_scaling_cards()

        The meta-scaling cards and the relevant metadata.

        :rtype: list[tuple[str, int]]

    .. property:: cards

        A list of all the cards in the deck. Duplicate cards appear multiple times.

        :type: Generator[str, None, None]

    .. property:: _removals

        A list of all the cards that were removed this run. This is implemented by the subclasses.

        :type: list[str]

    .. method:: get_removals()

        Generator to get the cards that were removed during the run.

        :type: Generator[:class:`CardData`, None, None]

    .. property:: has_removals

        Whether the run has had any cards removed (yet).

        :type: bool

    .. property:: removals

        A list of all the cards removed during the run. Duplicate cards appear multiple times.

        :type: Generator[str, None, None]

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

    .. property:: relics

        List all of the relics the run currently has.

        :type: list[:class:`RelicData`]

    .. property:: seed

        The run's seed. This calculates it from the integer saved in the JSON file, then caches it.

        :return: The seed like Spire displays it
        :type: str

    .. property:: is_seeded

        Whether the run is seeded.

        :type: bool

    .. property:: path

        List the path that the run took, with proper info accompanying it.

        :type: list[:class:`NodeData`]

    .. property:: modifiers

        Show all modifiers the run has.

        :type: list[str]

    .. property:: modifiers_with_desc

        Return a human-friendly output of the modifiers with a short description.

        :type: list[str]

    .. property:: score

        Get the run's score. This is implemented by the subclasses.

        :type: int

    .. property:: score_breakdown

        Get the score breakdown of the run. This is implemented by the subclasses.

        :type: list[str]

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

        :type: bool

    .. property:: verb

        Which verb to use for the run.

        :type: str

    .. property:: killed_by

        Which enemy encounter killed us, if relevant.

        :type: str or None

    .. property:: floor_reached

        The highest floor the run reached.

        :type: int

    .. property:: final_health

        The final health value of the run, in a (current, max) tuple.

        :type: tuple[int, int]

    .. property:: run_length

        How long the run lasted, in a human-readable format.

        :type: str

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

        :type: bool

    .. property:: current_health

        The run's current health.

        :type: int

    .. property:: max_health

        The run's max health.

        :type: int

    .. property:: current_gold

        The run's current gold count.

        :type: int

    .. property:: current_purge

        The current cost of the merchant's card removal service.

        :type: int

    .. property:: purge_totals

        How many cards were removed using the merchant's card removal service.

        :type: int

    .. property:: shop_prices

        The current shop prices. This is a tuple of 4 tuples; (cards, colorless, relics, potions). Each tuple is a tuple of range objects for the prices of the (common, uncommon, rare) rarity for each. The colorless tuple is only (uncommon, rare). The items can cost as low as `obj.start` and as high as `obj.stop`, slightly breaking Pythonic assumptions. This is fine.

        :type: tuple[tuple[range, range, range], tuple[range, range], tuple[range, range, range], tuple[range, range, range]]

    .. property:: current_floor

        The current floor of the run.

        :type: int

    .. property:: potion_chance

        The current chance for a potion to drop after a fight. This is a percentage.

        :type: int

    .. property:: rare_chance

        The current chance to see any rare card after. This is a tuple of (fight rewards, elite rewards, shop contents), indicating the odds for that particular point to have a rare card. This is a chance to see *any* rare card, not just one. This is a percentage.

        :type: tuple[float, float, float]

    .. method:: rare_chance_as_str()

        Same as the above, except as human-readable strings.

        :rtype: tuple[str, str, str]

    .. property:: upcoming_boss

        Which is the upcoming act boss. This only returns the first Act 3 boss, if relevant.

        :type: str

    .. property:: bottles

        The bottle relics and their bottled cards.

        :type: list[:class:`BottleRelic`]

    .. method:: _get_score_bonuses()

        Get the score bonuses for :attr:`score_breakdown`.

        :rtype: list[:class:`score.Score`]

    .. property:: monsters_killed

        How many regular enemies were killed.

        :type: int

    .. property:: act1_elites_killed

        How many Exordium elites were killed.

        :type: int

    .. property:: act2_elites_killed

        How many City elites were killed.

        :type: int

    .. property:: act3_elites_killed

        How many Beyond elites were killed.

        :type: int

    .. property:: perfect_elites

        How many elites were killed without taking damage.

        :type: int

    .. property:: perfect_bosses

        How many bosses were killed without taking damage.

        :rtype: int

    .. property:: has_overkill

        Whether we did 99 or more damage with a single attack.

        :type: bool

    .. property:: mystery_machine_counter

        How many unknown rooms were visited.

        :type: int

    .. property:: total_gold_gained

        How much gold has been acquired in the entire run.

        :type: int

    .. property:: has_combo

        Whether we played 20 or more cards in a single turn.

        :type: bool

    .. property:: act_num

        The act we are currently in.

        :type: int

    .. property:: deck_card_ids

        The internal card names, for score breakdown purposes.

        :type: list[str]

.. function:: save.get_savefile([context=None])
    :async:

    Get the current savefile, or None if no run is ongoing.

    :param context: The message context that triggered the call, or None.
    :type context: A TwitchIO or DiscordPY message context, or None
    :return: The current savefile or None
    :rtype: :class:`save.Savefile` or None

.. class:: KeysObtained

    .. attribute:: ruby_key_obtained

        Whether the Ruby key has been obtained.

        :type: bool

    .. attribute:: ruby_key_floor

        Which floor the Ruby key was obtained on, if available.

        :type: int

    .. attribute:: emerald_key_obtained

        Whether the Emerald key has been obtained.

        :type: bool

    .. attribute:: emerald_key_floor

        Which floor the Emerald key was obtained on, if available.

        :type: int

    .. attribute:: sapphire_key_obtained

        Whether the Sapphire key has been obtained.

        :type: bool

    .. attribute:: sapphire_key_floor

        Which floor the Sapphire key was obtained on, if available.

        :type: int

    .. method:: as_list()

        A list of all three keys and their values, in order Emerald, Ruby, Sapphire.

        :rtype: list[tuple[str, bool, int]]

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

        :type: str

    .. property:: name

        The in-game name of the relic.

        :type: str

.. class:: CardData(card, cards_list, meta)

    The class storing card information for a run.

    :param str card: The internal name of the card.
    :param cards_list: The list this card belongs to.
    :type cards_list: list[str]
    :param int meta: Meta-scaling information for the card.

    .. attribute:: internal

        The internal name of the card.

        :type: str

    .. attribute:: card

        The dataclass for the card's static information.

        :type: :class:`nameinternal.Card`

    .. attribute:: meta

        The meta-scaling information for the card.

        :type: int

    .. attribute:: upgrades

        The upgrade count for the card, 0 if unavailable.

        :type: int

    .. method:: as_cards()

        An iterable of the card's display name, repeated so all cards happen multiple times.

        :type: Generator[str, None, None]

    .. property:: color

        The card's color. Modded characters may not return an actual color.

        :type: str

    .. property:: type

        The card's type.

        :type: str

    .. property:: type_safe

        The card's type, safe for page display. This will be either "Attack", "Skill", or "Power".

        :type: str

    .. property:: rarity

        The card's rarity.

        :type: str

    .. property:: rarity_safe

        The card's rarity, safe for page display. This will be either "Common", "Uncommon", or "Rare".

        :type: str

    .. property:: count

        How many times the card appears in the provided list.

        :type: int

    .. property:: name

        The name of the card, as it appears in the game.

        :type: str

    .. property:: display_name

        The card as it would show in the deck display. This includes the card's count if it is greater than 1.

        :type: str
