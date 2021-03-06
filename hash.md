```
int findHash(const char *name)
{
    ULL k = convertDecfromStr(name);
    RINT h = k % MOD;
    RINT cnt = MOD;
 
    while (hash_name[h].decName != 0 && cnt--)
    {
        if (hash_name[h].decName == k && hash_name[h].firstName == firstName)
            return h;
        h = (h + 1) % MOD;
    }
    return -1;
}
 
int addHash(const char *name)
{
    ULL k = convertDecfromStr(name);
    RINT h = k % MOD;
    while (hash_name[h].decName != 0)
    {
        if (hash_name[h].decName == k && hash_name[h].firstName == firstName)
            return h;
        h = (h + 1) % MOD;
    }
    hash_name[h].decName = k;
    hash_name[h].firstName = firstName;
    return h;
}
 
 
```
