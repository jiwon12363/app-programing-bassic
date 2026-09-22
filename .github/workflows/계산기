import 'dart:math' as math;

import 'package:flutter/material.dart';
import 'package:flutter/services.dart';

void main() {
  runApp(const CalculatorApp());
}

class CalculatorApp extends StatelessWidget {
  const CalculatorApp({super.key});

  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      debugShowCheckedModeBanner: false,
      theme: ThemeData(
        brightness: Brightness.dark,
        scaffoldBackgroundColor: const Color(0xff202020),
        fontFamily: 'Segoe UI',
        colorScheme: ColorScheme.fromSeed(
          seedColor: const Color(0xff76b9ed),
          brightness: Brightness.dark,
        ),
      ),
      home: const CalculatorPage(),
    );
  }
}

class CalculatorPage extends StatefulWidget {
  const CalculatorPage({super.key});

  @override
  State<CalculatorPage> createState() => _CalculatorPageState();
}

class _CalculatorPageState extends State<CalculatorPage> {
  String _display = '0';
  String _expression = '';
  double? _storedValue;
  String? _operator;
  bool _shouldResetDisplay = false;
  final FocusNode _keyboardFocusNode = FocusNode();

  @override
  void dispose() {
    _keyboardFocusNode.dispose();
    super.dispose();
  }

  void _handleKeyEvent(KeyEvent event) {
    if (event is KeyUpEvent) return;

    final label = switch (event.logicalKey) {
      LogicalKeyboardKey.digit0 || LogicalKeyboardKey.numpad0 => '0',
      LogicalKeyboardKey.digit1 || LogicalKeyboardKey.numpad1 => '1',
      LogicalKeyboardKey.digit2 || LogicalKeyboardKey.numpad2 => '2',
      LogicalKeyboardKey.digit3 || LogicalKeyboardKey.numpad3 => '3',
      LogicalKeyboardKey.digit4 || LogicalKeyboardKey.numpad4 => '4',
      LogicalKeyboardKey.digit5 || LogicalKeyboardKey.numpad5 => '5',
      LogicalKeyboardKey.digit6 || LogicalKeyboardKey.numpad6 => '6',
      LogicalKeyboardKey.digit7 || LogicalKeyboardKey.numpad7 => '7',
      LogicalKeyboardKey.digit8 || LogicalKeyboardKey.numpad8 => '8',
      LogicalKeyboardKey.digit9 || LogicalKeyboardKey.numpad9 => '9',
      LogicalKeyboardKey.period || LogicalKeyboardKey.numpadDecimal => '.',
      LogicalKeyboardKey.add || LogicalKeyboardKey.numpadAdd => '+',
      LogicalKeyboardKey.minus || LogicalKeyboardKey.numpadSubtract => '−',
      LogicalKeyboardKey.numpadMultiply => '×',
      LogicalKeyboardKey.slash || LogicalKeyboardKey.numpadDivide => '÷',
      LogicalKeyboardKey.enter ||
      LogicalKeyboardKey.numpadEnter ||
      LogicalKeyboardKey.equal => '=',
      LogicalKeyboardKey.backspace || LogicalKeyboardKey.delete => '⌫',
      LogicalKeyboardKey.escape => 'C',
      _ => null,
    };

    if (label != null) _press(label);
  }

  void _press(String label) {
    setState(() {
      if (RegExp(r'^\d$').hasMatch(label) || label == '.') {
        _enterNumber(label);
      } else if (label == 'C') {
        _clear();
      } else if (label == '⌫') {
        _deleteLast();
      } else if (label == '±') {
        _toggleSign();
      } else if (label == '%') {
        _display = _format(double.parse(_display) / 100);
      } else if (label == '¹⁄ₓ') {
        _display = _format(1 / (double.tryParse(_display) ?? 0));
        _shouldResetDisplay = true;
      } else if (label == 'x²') {
        final value = double.tryParse(_display) ?? 0;
        _display = _format(value * value);
        _shouldResetDisplay = true;
      } else if (label == '√x') {
        _display = _format(math.sqrt(double.tryParse(_display) ?? 0));
        _shouldResetDisplay = true;
      } else if (label == '=') {
        _calculate();
      } else {
        _chooseOperator(label);
      }
    });
  }

  void _enterNumber(String value) {
    if (_shouldResetDisplay) {
      _display = value == '.' ? '0.' : value;
      _shouldResetDisplay = false;
      return;
    }
    if (value == '.' && _display.contains('.')) return;
    _display = _display == '0' && value != '.' ? value : '$_display$value';
  }

  void _chooseOperator(String operator) {
    final currentValue = double.tryParse(_display) ?? 0;
    if (_storedValue != null && _operator != null && !_shouldResetDisplay) {
      _display = _format(
        _applyOperation(_storedValue!, currentValue, _operator!),
      );
    }
    _storedValue = double.tryParse(_display) ?? 0;
    _operator = operator;
    _expression = '${_format(_storedValue!)} $operator';
    _shouldResetDisplay = true;
  }

  void _calculate() {
    if (_storedValue == null || _operator == null) return;
    final currentValue = double.tryParse(_display) ?? 0;
    final result = _applyOperation(_storedValue!, currentValue, _operator!);
    _expression =
        '${_format(_storedValue!)} $_operator ${_format(currentValue)} =';
    _display = _format(result);
    _storedValue = null;
    _operator = null;
    _shouldResetDisplay = true;
  }

  double _applyOperation(double first, double second, String operator) {
    switch (operator) {
      case '+':
        return first + second;
      case '−':
        return first - second;
      case '×':
        return first * second;
      case '÷':
        return second == 0 ? double.nan : first / second;
      default:
        return second;
    }
  }

  void _clear() {
    _display = '0';
    _expression = '';
    _storedValue = null;
    _operator = null;
    _shouldResetDisplay = false;
  }

  void _deleteLast() {
    if (_shouldResetDisplay || _display.length <= 1) {
      _display = '0';
      return;
    }
    _display = _display.substring(0, _display.length - 1);
    if (_display == '-') _display = '0';
  }

  void _toggleSign() {
    if (_display == '0') return;
    _display = _display.startsWith('-') ? _display.substring(1) : '-$_display';
  }

  String _format(double value) {
    if (value.isNaN || value.isInfinite) return '오류';
    if (value == value.truncateToDouble()) return value.toInt().toString();
    return value
        .toStringAsFixed(8)
        .replaceFirst(RegExp(r'0+$'), '')
        .replaceFirst(RegExp(r'\.$'), '');
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        backgroundColor: const Color(0xff202020),
        elevation: 0,
        titleSpacing: 20,
        title: const Row(
          children: [
            Icon(Icons.calculate_outlined, size: 22),
            SizedBox(width: 12),
            Text(
              '계산기',
              style: TextStyle(fontSize: 16, fontWeight: FontWeight.w600),
            ),
          ],
        ),
        actions: [
          IconButton(
            onPressed: () {},
            icon: const Icon(Icons.history_outlined),
            tooltip: '기록',
          ),
          IconButton(
            onPressed: () {},
            icon: const Icon(Icons.more_horiz),
            tooltip: '더 보기',
          ),
          const SizedBox(width: 8),
        ],
      ),
      body: KeyboardListener(
        focusNode: _keyboardFocusNode,
        autofocus: true,
        onKeyEvent: _handleKeyEvent,
        child: SafeArea(
          child: Center(
            child: ConstrainedBox(
              constraints: const BoxConstraints(maxWidth: 560),
              child: Padding(
                padding: const EdgeInsets.fromLTRB(12, 4, 12, 12),
                child: Column(
                  children: [
                    const Align(
                      alignment: Alignment.centerLeft,
                      child: Text(
                        '표준',
                        style: TextStyle(
                          fontSize: 22,
                          fontWeight: FontWeight.w600,
                        ),
                      ),
                    ),
                    const SizedBox(height: 20),
                    Expanded(
                      flex: 3,
                      child: Align(
                        alignment: Alignment.bottomRight,
                        child: Column(
                          mainAxisAlignment: MainAxisAlignment.end,
                          crossAxisAlignment: CrossAxisAlignment.end,
                          children: [
                            Text(
                              _expression,
                              style: TextStyle(
                                color: Colors.white54,
                                fontSize: 16,
                              ),
                            ),
                            const SizedBox(height: 8),
                            FittedBox(
                              fit: BoxFit.scaleDown,
                              child: Text(
                                _display,
                                style: const TextStyle(
                                  fontSize: 48,
                                  fontWeight: FontWeight.w600,
                                ),
                              ),
                            ),
                          ],
                        ),
                      ),
                    ),
                    const SizedBox(height: 18),
                    Row(
                      children: [
                        _memoryButton('MC'),
                        _memoryButton('MR'),
                        _memoryButton('M+'),
                        _memoryButton('M-'),
                        _memoryButton('MS'),
                      ],
                    ),
                    const SizedBox(height: 8),
                    Expanded(flex: 6, child: _keypad()),
                  ],
                ),
              ),
            ),
          ),
        ),
      ),
    );
  }

  Widget _keypad() {
    const keys = [
      ['%', 'CE', 'C', '⌫'],
      ['¹⁄ₓ', 'x²', '√x', '÷'],
      ['7', '8', '9', '×'],
      ['4', '5', '6', '−'],
      ['1', '2', '3', '+'],
      ['±', '0', '.', '='],
    ];
    return Column(
      children: keys.map((row) {
        return Expanded(
          child: Row(children: row.map((label) => _key(label)).toList()),
        );
      }).toList(),
    );
  }

  Widget _key(String label) {
    final isOperator = ['÷', '×', '−', '+', '='].contains(label);
    final isNumber = RegExp(r'^\d$').hasMatch(label) || label == '.';
    return Expanded(
      child: Padding(
        padding: const EdgeInsets.all(2),
        child: Material(
          color: isOperator
              ? const Color(0xff345b79)
              : isNumber
              ? const Color(0xff303030)
              : const Color(0xff292929),
          borderRadius: BorderRadius.circular(4),
          child: InkWell(
            onTap: label == 'CE' ? () => _press('C') : () => _press(label),
            borderRadius: BorderRadius.circular(4),
            child: Center(
              child: Text(
                label,
                style: TextStyle(
                  fontSize: isNumber ? 21 : 17,
                  fontWeight: isOperator ? FontWeight.w600 : FontWeight.w400,
                ),
              ),
            ),
          ),
        ),
      ),
    );
  }

  Widget _memoryButton(String label) {
    return Expanded(
      child: TextButton(
        onPressed: null,
        child: Text(label, style: const TextStyle(fontSize: 12)),
      ),
    );
  }
}
