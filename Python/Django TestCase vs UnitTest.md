# What is the difference between Django TestCase and standard UnitTest

---

Main difference between the Django test case (which inherits from the default python unittest library) is that TestCase wraps each unit test into a transaction. This is how a test case is run in Django:

1. Before every test a transaction is started
2. The fixtures (if existing) are loaded
3. The setUp method is run
4. No commits performed as tests run
5. The tearDown method is run
6. Rollback is performed i.e. DB is flushed

For more info on how Django test framework works: https://django-testing-docs.readthedocs.io/en/latest
