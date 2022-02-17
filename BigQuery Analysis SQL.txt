WITH
  STORIES AS (
  SELECT
    st.BY AS story_username,
    COUNT(st.id) AS no_of_times_this_username_has_publsihed_a_story,
    SUM(st.score)/COUNT(st.score) AS avg_score
  FROM
    `bigquery-public-data.hacker_news.stories` st
  GROUP BY
    st.BY),
  COMMENTS AS (
  SELECT
    ct.BY AS comment_username,
    COUNT(ct.id) AS comment_count
  FROM
    `bigquery-public-data.hacker_news.comments` ct
  GROUP BY
    ct.BY),
  COUNT_GREATER_THAN_TEN AS (
  SELECT
    *
  FROM
    STORIES
  LEFT JOIN
    COMMENTS
  ON
    STORIES.story_username = COMMENTS.comment_username
  WHERE
    comment_count>=10)
SELECT
  story_username,
  no_of_times_this_username_has_publsihed_a_story,
  avg_score,
  comment_username,
  comment_count,
  ROW_NUMBER() OVER(ORDER BY COUNT_GREATER_THAN_TEN.avg_score DESC) RANK
FROM
  COUNT_GREATER_THAN_TEN
