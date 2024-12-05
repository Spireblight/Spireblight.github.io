All-purpose utility functions
=============================

.. module:: utils

.. function:: get_req_data(req, *keys)
    :async:

    Validate POST requests and extract data.

    :rtype: list[str]

    :param req: The incoming POST request.
    :type req: aiohttp.web.Request

    :param keys: The keys to extract from the request.
    :type keys: str

    All valid POST requests must include a `key` key, which must be exactly
    equal to the stored secret on the server. If it is not present, the client
    will receive an HTTP 401 "Unauthorized". If it is present and does not match,
    HTTP 403 "Forbidden" will be raised instead. If the server does not possess a
    secret, an HTTP 501 "Not Implemented" will be raised.

    The secret may not be URL-encoded. For a production environment, the use of
    `secrets.token_urlsafe` is strongly recommended. The main instance uses a
    secret with 64 random bits.

    If the secret is present and matching, the values matching the provided keys
    will be extracted, decoded, and returned in a list. No further parsing is
    done on the data; it is the responsibility of the caller to do so.
