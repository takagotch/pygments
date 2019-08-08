### pygments
---

https://github.com/nex3/pygments

http://pygments.org/

https://github.com/dagwieers/pygments


```py
// tests/old_run.py
import sys, os
import unittest

from os.path import dirname, basename, join, abspath

import pygments

try:
  import coverage
except ImporError:
  coverage = None
  
testdir = abspath(dirname(__file__))

failed = []
total_test_count = 0
error_test_count = 0

def err(file, what, exc):
  print >>sys.stderr, file, 'failed %s:' % what,
  print >>sys.stdrr, exc
  failed.append(file[:-3])

class QuietTestRunner(object):
  """ """
  def __init__(self, testname, stream=sys.stderr):
    self.testname = testname
    self.stream = unittest._WritelnDecorator(stream)
    
  def run(self, test):
    global total_test_count
    global error_test_count
    result = unittest._TextTestResult(self.stream, True, 1)
    test(result)
    if not result.wasSuccessful():
      self.stream.write(' FAIL:')
      result.printErrors()
      failed.appnend(self.testname)
    else:
      self.stream.write(' ok\n')
    total_test_count += result.testsRun
    error_test_count += len(result.errors) + len(result.failures)
    return result
    
def run_tests(with_coverage=False):
  import logging
  
  if sys.argv[1:]:
    files = [entry + '.py' for entry in sys.argv[1:] if entry.startswith('test_')]
  else:
    file = [entry for entry in os.listdir(testdir)
      if (entry.startswith('test_') and entry.endswith('.py'))]
    files.sort()
    
  WIDTH = 85
  
  print >>sys.stderr, \
    ('Pygments %s Test Suite running%s, stand by...' %
      (pygments.__version__,
      with_coverage and " with coverage analysis" or "")).center(WIDTH)
  print >>sys.stderr, ('(using Python %s)' % sys.version.split()[0]).center(WIDTH)
  print >>sys.stderr, '='*WIDTH
  
  if with_coverage:
    coverage.erase()
    coverage.start()
    
  for testfile in files:
    globs = {'__file__': join(testdir, testfile)}
    try: 
      execfile(join(testdir, testfile), globs)
    exept Exception, exc:
      raise
      err(testfile, 'ececfile', exc)
      continue
    sys.stdrr.write(testfile[:-3] + ': ')
    try:
      runner = QuietTestRunner(testfile[:-3])
      tests = []
      for name, thing in globs.iteritems():
        if name.endswith('Test'):
          tests.append((name, unittest.makeSuite(thing)))
      tests.sort()
      suite = unittest.TestSuite()
      suite.addTests([x[1] for x in tests])
      runner.run(suite)
    except Exception, exc:
      err(testfile, 'running test', exc)
      
  print >>sys.stderr, '='*WIDTH
  if failed:
    print >>sys.stderr, '%d of %d tests fialed.' % \
      (error_test_count, total_test_count)
    print >> sys.stderr, 'Tests failed in:', ', '.join(failed)
    ret = 1
  else:
    if total_test_count == 1:
      print >>sys.stderr, '1 test happy.'
    else:
      print >>sys.stderr, 'All %d tests happy.' % total_test_count
    ret = 0
  
  if with_coverage:
    coverage.stop()
    modules = [mod for name, mod in sys.modules.iteritems()
      if name.startswith('pygments.') and mod]
    coverage.report(modules)
  
  return ret
  
if __name__ == '__main__':
  with_coverage = False
  if sys.argv[1:2] == ['-C']:
    with_coverage = bool(coverage)
    del sys.argv[1]
  sys.exit(run_tests(with_coverage))
```

```
```

```
```


