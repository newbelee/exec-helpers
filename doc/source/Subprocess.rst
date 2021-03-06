.. Subprocess

API: Subprocess
===============

.. py:module:: exec_helpers
.. py:currentmodule:: exec_helpers

.. py:class:: Subprocess()

    .. py:method:: __init__(logger, log_mask_re=None)

        ExecHelper global API.

        :param log_mask_re: regex lookup rule to mask command for logger. all MATCHED groups will be replaced by '<*masked*>'
        :type log_mask_re: typing.Optional[str]

        .. versionchanged:: 1.2.0 log_mask_re regex rule for masking cmd

    .. py:attribute:: log_mask_re

        ``typing.Optional[str]``

        regex lookup rule to mask command for logger. all MATCHED groups will be replaced by '<*masked*>'

    .. py:attribute:: lock

        ``threading.RLock``

    .. py:method:: __enter__()

        Open context manager

        .. versionchanged:: 1.1.0 lock on enter

    .. py:method:: __exit__(self, exc_type, exc_val, exc_tb)

        Close context manager

        .. versionchanged:: 1.1.0 release lock on exit

    .. py:method:: execute_async(command, stdin=None, open_stdout=True, open_stderr=True, verbose=False, log_mask_re=None, **kwargs)

        Execute command in async mode and return Popen with IO objects.

        :param command: Command for execution
        :type command: str
        :param stdin: pass STDIN text to the process
        :type stdin: ``typing.Union[str, bytes, bytearray, None]``
        :param open_stdout: open STDOUT stream for read
        :type open_stdout: ``bool``
        :param open_stderr: open STDERR stream for read
        :type open_stderr: ``bool``
        :param verbose: produce verbose log record on command call
        :type verbose: ``bool``
        :param log_mask_re: regex lookup rule to mask command for logger.
                            all MATCHED groups will be replaced by '<*masked*>'
        :type log_mask_re: ``typing.Optional[str]``
        :rtype: ``typing.Tuple[subprocess.Popen, None, typing.Optional[typing.IO], typing.Optional[typing.IO], ]``

        .. versionadded:: 1.2.0

    .. py:method:: execute(command, verbose=False, timeout=1*60*60, **kwargs)

        Execute command and wait for return code.

        :param command: Command for execution
        :type command: ``str``
        :param verbose: Produce log.info records for command call and output
        :type verbose: ``bool``
        :param timeout: Timeout for command execution.
        :type timeout: ``typing.Union[int, float, None]``
        :rtype: ExecResult
        :raises ExecHelperTimeoutError: Timeout exceeded

        .. note:: stdin channel is closed after the input processing
        .. versionchanged:: 1.1.0 make method
        .. versionchanged:: 1.2.0 open_stdout and open_stderr flags
        .. versionchanged:: 1.2.0 default timeout 1 hour
        .. versionchanged:: 1.2.0 stdin data

    .. py:method:: check_call(command, verbose=False, timeout=1*60*60, error_info=None, expected=None, raise_on_err=True, **kwargs)

        Execute command and check for return code.

        :param command: Command for execution
        :type command: ``str``
        :param verbose: Produce log.info records for command call and output
        :type verbose: ``bool``
        :param timeout: Timeout for command execution.
        :type timeout: ``typing.Union[int, float, None]``
        :param error_info: Text for error details, if fail happens
        :type error_info: ``typing.Optional[str]``
        :param expected: expected return codes (0 by default)
        :type expected: ``typing.Optional[typing.Iterable[int]]``
        :param raise_on_err: Raise exception on unexpected return code
        :type raise_on_err: ``bool``
        :rtype: ExecResult
        :raises ExecHelperTimeoutError: Timeout exceeded
        :raises CalledProcessError: Unexpected exit code

        .. versionchanged:: 1.1.0 make method
        .. versionchanged:: 1.2.0 default timeout 1 hour

    .. py:method:: check_stderr(command, verbose=False, timeout=1*60*60, error_info=None, raise_on_err=True, **kwargs)

        Execute command expecting return code 0 and empty STDERR.

        :param command: Command for execution
        :type command: ``str``
        :param verbose: Produce log.info records for command call and output
        :type verbose: ``bool``
        :param timeout: Timeout for command execution.
        :type timeout: ``typing.Union[int, float, None]``
        :param error_info: Text for error details, if fail happens
        :type error_info: ``typing.Optional[str]``
        :param raise_on_err: Raise exception on unexpected return code
        :type raise_on_err: ``bool``
        :rtype: ExecResult
        :raises ExecHelperTimeoutError: Timeout exceeded
        :raises CalledProcessError: Unexpected exit code or stderr presents

        .. note:: expected return codes can be overridden via kwargs.

        .. versionchanged:: 1.1.0 make method
        .. versionchanged:: 1.2.0 default timeout 1 hour
