# wp-performance-engineering-or-database-optimization.-
High-performance PHP and SQL scripts for optimizing large-scale WordPress databases (100k+ records). 
-- 1. Check for 'Autoload' Bloat (Crucial for 1s load times)
-- Identifies large options that slow down every single page load.
SELECT option_name, length(option_value) AS option_value_length 
FROM wp_options 
WHERE autoload = 'yes' 
ORDER BY option_value_length DESC 
LIMIT 20;

-- 2. "Database Surgery": Clean up massive Orphaned Metadata
-- Removes metadata from deleted posts that often scales into the millions.
DELETE pm FROM wp_postmeta pm 
LEFT JOIN wp_posts p ON p.ID = pm.post_id 
WHERE p.ID IS NULL;

-- 3. Clear transient timeouts and expired session data
-- Essential for sites handling 100k+ active user records.
DELETE FROM wp_options 
WHERE option_name LIKE '_transient_timeout_%' 
OR option_name LIKE '_site_transient_timeout_%';

-- 4. Optimize the table to reclaim disk space after cleanup
OPTIMIZE TABLE wp_options, wp_postmeta, wp_posts;
